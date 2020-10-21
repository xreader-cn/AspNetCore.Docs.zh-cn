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
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326591"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="d6ee3-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="d6ee3-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d6ee3-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d6ee3-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d6ee3-107">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d6ee3-108">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d6ee3-109">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d6ee3-110">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d6ee3-111">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d6ee3-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d6ee3-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d6ee3-114">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d6ee3-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d6ee3-116">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d6ee3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d6ee3-117">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d6ee3-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-118">MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-119">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d6ee3-120">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d6ee3-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-122">Configure server options</span></span>

<span data-ttu-id="d6ee3-123">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d6ee3-124">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-124">Option</span></span> | <span data-ttu-id="d6ee3-125">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-125">Default Value</span></span> | <span data-ttu-id="d6ee3-126">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d6ee3-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-127">30 seconds</span></span> | <span data-ttu-id="d6ee3-128">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d6ee3-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d6ee3-130">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-131">15 seconds</span></span> | <span data-ttu-id="d6ee3-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d6ee3-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-134">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-135">15 seconds</span></span> | <span data-ttu-id="d6ee3-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d6ee3-137">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d6ee3-138">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d6ee3-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="d6ee3-139">All installed protocols</span></span> | <span data-ttu-id="d6ee3-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d6ee3-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d6ee3-142">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d6ee3-143">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d6ee3-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d6ee3-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d6ee3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-146">32 KB</span></span> | <span data-ttu-id="d6ee3-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="d6ee3-148">1</span><span class="sxs-lookup"><span data-stu-id="d6ee3-148">1</span></span> | <span data-ttu-id="d6ee3-149">每个客户端可以在进行排队之前并行调用的最大集线器方法数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="d6ee3-150">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d6ee3-151">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d6ee3-152">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d6ee3-153">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d6ee3-154">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d6ee3-155">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d6ee3-156">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-156">Option</span></span> | <span data-ttu-id="d6ee3-157">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-157">Default Value</span></span> | <span data-ttu-id="d6ee3-158">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d6ee3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-159">32 KB</span></span> | <span data-ttu-id="d6ee3-160">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d6ee3-161">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d6ee3-162">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d6ee3-163">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d6ee3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-164">32 KB</span></span> | <span data-ttu-id="d6ee3-165">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d6ee3-166">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d6ee3-167">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-167">All Transports are enabled.</span></span> | <span data-ttu-id="d6ee3-168">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d6ee3-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-169">See below.</span></span> | <span data-ttu-id="d6ee3-170">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d6ee3-171">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-171">See below.</span></span> | <span data-ttu-id="d6ee3-172">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d6ee3-173">0</span><span class="sxs-lookup"><span data-stu-id="d6ee3-173">0</span></span> | <span data-ttu-id="d6ee3-174">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d6ee3-175">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d6ee3-176">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d6ee3-177">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-177">Option</span></span> | <span data-ttu-id="d6ee3-178">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-178">Default Value</span></span> | <span data-ttu-id="d6ee3-179">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d6ee3-180">90秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-180">90 seconds</span></span> | <span data-ttu-id="d6ee3-181">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d6ee3-182">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d6ee3-183">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d6ee3-184">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-184">Option</span></span> | <span data-ttu-id="d6ee3-185">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-185">Default Value</span></span> | <span data-ttu-id="d6ee3-186">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d6ee3-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-187">5 seconds</span></span> | <span data-ttu-id="d6ee3-188">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d6ee3-189">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d6ee3-190">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d6ee3-191">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-191">Configure client options</span></span>

<span data-ttu-id="d6ee3-192">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d6ee3-193">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d6ee3-194">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="d6ee3-194">Configure logging</span></span>

<span data-ttu-id="d6ee3-195">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d6ee3-196">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d6ee3-197">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-198">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d6ee3-199">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d6ee3-200">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d6ee3-201">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d6ee3-202">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d6ee3-203">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d6ee3-204">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d6ee3-205">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d6ee3-206">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d6ee3-207">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="d6ee3-208">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d6ee3-209">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d6ee3-210">String</span><span class="sxs-lookup"><span data-stu-id="d6ee3-210">String</span></span>                      | <span data-ttu-id="d6ee3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d6ee3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d6ee3-212">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d6ee3-213">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d6ee3-214">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d6ee3-215">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d6ee3-216">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d6ee3-217">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d6ee3-218">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d6ee3-219">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d6ee3-220">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d6ee3-221">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="d6ee3-221">Configure allowed transports</span></span>

<span data-ttu-id="d6ee3-222">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d6ee3-223">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d6ee3-224">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="d6ee3-225">例如，禁用 Server-Sent 事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d6ee3-226">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d6ee3-227">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d6ee3-228">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d6ee3-229">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d6ee3-230">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d6ee3-231">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="d6ee3-231">Configure bearer authentication</span></span>

<span data-ttu-id="d6ee3-232">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d6ee3-233">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d6ee3-234">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在 Server-Sent 事件和 websocket 请求)  (具体应用标头的能力。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d6ee3-235">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d6ee3-236">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d6ee3-237">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d6ee3-238">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d6ee3-239">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d6ee3-240">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d6ee3-241">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d6ee3-242">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-244">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-244">Option</span></span> | <span data-ttu-id="d6ee3-245">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-245">Default value</span></span> | <span data-ttu-id="d6ee3-246">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d6ee3-247">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-248">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-248">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-249">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-250">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-251">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-252">15 seconds</span></span> | <span data-ttu-id="d6ee3-253">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-254">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-255">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-256">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-257">15 seconds</span></span> | <span data-ttu-id="d6ee3-258">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-259">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-260">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d6ee3-261">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-263">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-263">Option</span></span> | <span data-ttu-id="d6ee3-264">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-264">Default value</span></span> | <span data-ttu-id="d6ee3-265">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d6ee3-266">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-267">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-267">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-268">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d6ee3-269">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-270">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d6ee3-271">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-272">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-273">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-274">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-275">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-276">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-276">Option</span></span> | <span data-ttu-id="d6ee3-277">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-277">Default value</span></span> | <span data-ttu-id="d6ee3-278">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d6ee3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d6ee3-280">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-281">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-281">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-282">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-283">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-284">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d6ee3-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-285">15 seconds</span></span> | <span data-ttu-id="d6ee3-286">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-287">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-288">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-289">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d6ee3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d6ee3-291">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-292">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-293">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-294">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d6ee3-295">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-295">Configure additional options</span></span>

<span data-ttu-id="d6ee3-296">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-298">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-298">.NET Option</span></span> |  <span data-ttu-id="d6ee3-299">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-299">Default value</span></span> | <span data-ttu-id="d6ee3-300">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-301">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d6ee3-302">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-303">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-304">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d6ee3-305">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-305">Empty</span></span> | <span data-ttu-id="d6ee3-306">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d6ee3-307">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-307">Empty</span></span> | <span data-ttu-id="d6ee3-308">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d6ee3-309">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-309">Empty</span></span> | <span data-ttu-id="d6ee3-310">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d6ee3-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-311">5 seconds</span></span> | <span data-ttu-id="d6ee3-312">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-312">WebSockets only.</span></span> <span data-ttu-id="d6ee3-313">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d6ee3-314">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d6ee3-315">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-315">Empty</span></span> | <span data-ttu-id="d6ee3-316">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d6ee3-317">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d6ee3-318">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="d6ee3-319">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d6ee3-320">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d6ee3-321">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="d6ee3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d6ee3-322">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d6ee3-323">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d6ee3-324">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d6ee3-325">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d6ee3-326">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-328">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-328">JavaScript Option</span></span> | <span data-ttu-id="d6ee3-329">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-329">Default Value</span></span> | <span data-ttu-id="d6ee3-330">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d6ee3-331">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d6ee3-332">一个 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> 值，该值指定用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="d6ee3-333">每个 HTTP 请求发送的标头的字典。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="d6ee3-334">在浏览器中发送标头对于 Websocket 或流不起作用 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d6ee3-335">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d6ee3-336">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-337">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-338">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="d6ee3-339">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="d6ee3-340">Azure App Service cookie 将用于粘滞会话，并且需要启用此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="d6ee3-341">有关 CORS 的详细信息 SignalR ，请参阅 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-342">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-342">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-343">Java 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-343">Java Option</span></span> | <span data-ttu-id="d6ee3-344">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-344">Default Value</span></span> | <span data-ttu-id="d6ee3-345">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-346">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d6ee3-347">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-348">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-349">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d6ee3-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d6ee3-351">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-351">Empty</span></span> | <span data-ttu-id="d6ee3-352">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d6ee3-353">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d6ee3-354">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="d6ee3-355">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d6ee3-356">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6ee3-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d6ee3-357">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-358">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d6ee3-359">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d6ee3-360">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d6ee3-361">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d6ee3-362">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d6ee3-363">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d6ee3-364">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d6ee3-365">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d6ee3-366">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d6ee3-367">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d6ee3-368">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d6ee3-369">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d6ee3-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d6ee3-370">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d6ee3-371">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-371">MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-372">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d6ee3-373">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-374">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d6ee3-375">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-375">Configure server options</span></span>

<span data-ttu-id="d6ee3-376">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d6ee3-377">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-377">Option</span></span> | <span data-ttu-id="d6ee3-378">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-378">Default Value</span></span> | <span data-ttu-id="d6ee3-379">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d6ee3-380">30 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-380">30 seconds</span></span> | <span data-ttu-id="d6ee3-381">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d6ee3-382">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d6ee3-383">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-384">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-384">15 seconds</span></span> | <span data-ttu-id="d6ee3-385">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d6ee3-386">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-387">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-388">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-388">15 seconds</span></span> | <span data-ttu-id="d6ee3-389">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d6ee3-390">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d6ee3-391">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d6ee3-392">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="d6ee3-392">All installed protocols</span></span> | <span data-ttu-id="d6ee3-393">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-393">Protocols supported by this hub.</span></span> <span data-ttu-id="d6ee3-394">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d6ee3-395">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d6ee3-396">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d6ee3-397">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d6ee3-398">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d6ee3-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-399">32 KB</span></span> | <span data-ttu-id="d6ee3-400">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d6ee3-401">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d6ee3-402">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d6ee3-403">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d6ee3-404">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d6ee3-405">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d6ee3-406">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d6ee3-407">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-407">Option</span></span> | <span data-ttu-id="d6ee3-408">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-408">Default Value</span></span> | <span data-ttu-id="d6ee3-409">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d6ee3-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-410">32 KB</span></span> | <span data-ttu-id="d6ee3-411">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d6ee3-412">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d6ee3-413">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d6ee3-414">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d6ee3-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-415">32 KB</span></span> | <span data-ttu-id="d6ee3-416">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d6ee3-417">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d6ee3-418">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-418">All Transports are enabled.</span></span> | <span data-ttu-id="d6ee3-419">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d6ee3-420">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-420">See below.</span></span> | <span data-ttu-id="d6ee3-421">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d6ee3-422">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-422">See below.</span></span> | <span data-ttu-id="d6ee3-423">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d6ee3-424">0</span><span class="sxs-lookup"><span data-stu-id="d6ee3-424">0</span></span> | <span data-ttu-id="d6ee3-425">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d6ee3-426">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d6ee3-427">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d6ee3-428">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-428">Option</span></span> | <span data-ttu-id="d6ee3-429">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-429">Default Value</span></span> | <span data-ttu-id="d6ee3-430">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d6ee3-431">90秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-431">90 seconds</span></span> | <span data-ttu-id="d6ee3-432">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d6ee3-433">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d6ee3-434">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d6ee3-435">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-435">Option</span></span> | <span data-ttu-id="d6ee3-436">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-436">Default Value</span></span> | <span data-ttu-id="d6ee3-437">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d6ee3-438">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-438">5 seconds</span></span> | <span data-ttu-id="d6ee3-439">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d6ee3-440">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d6ee3-441">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d6ee3-442">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-442">Configure client options</span></span>

<span data-ttu-id="d6ee3-443">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d6ee3-444">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d6ee3-445">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="d6ee3-445">Configure logging</span></span>

<span data-ttu-id="d6ee3-446">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d6ee3-447">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d6ee3-448">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-449">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d6ee3-450">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d6ee3-451">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d6ee3-452">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d6ee3-453">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d6ee3-454">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d6ee3-455">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d6ee3-456">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d6ee3-457">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d6ee3-458">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-458">The following table lists the available log levels.</span></span> <span data-ttu-id="d6ee3-459">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d6ee3-460">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d6ee3-461">String</span><span class="sxs-lookup"><span data-stu-id="d6ee3-461">String</span></span>                      | <span data-ttu-id="d6ee3-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d6ee3-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d6ee3-463">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d6ee3-464">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d6ee3-465">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d6ee3-466">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d6ee3-467">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d6ee3-468">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d6ee3-469">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d6ee3-470">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d6ee3-471">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d6ee3-472">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="d6ee3-472">Configure allowed transports</span></span>

<span data-ttu-id="d6ee3-473">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d6ee3-474">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d6ee3-475">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-475">All transports are enabled by default.</span></span>

<span data-ttu-id="d6ee3-476">例如，禁用 Server-Sent 事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d6ee3-477">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d6ee3-478">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d6ee3-479">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d6ee3-480">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d6ee3-481">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d6ee3-482">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="d6ee3-482">Configure bearer authentication</span></span>

<span data-ttu-id="d6ee3-483">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d6ee3-484">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d6ee3-485">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在 Server-Sent 事件和 websocket 请求)  (具体应用标头的能力。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d6ee3-486">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d6ee3-487">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d6ee3-488">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d6ee3-489">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d6ee3-490">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d6ee3-491">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d6ee3-492">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d6ee3-493">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-494">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-495">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-495">Option</span></span> | <span data-ttu-id="d6ee3-496">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-496">Default value</span></span> | <span data-ttu-id="d6ee3-497">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d6ee3-498">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-499">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-499">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-500">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-501">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-502">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-503">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-503">15 seconds</span></span> | <span data-ttu-id="d6ee3-504">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-505">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-506">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-507">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-508">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-508">15 seconds</span></span> | <span data-ttu-id="d6ee3-509">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-510">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-511">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d6ee3-512">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-514">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-514">Option</span></span> | <span data-ttu-id="d6ee3-515">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-515">Default value</span></span> | <span data-ttu-id="d6ee3-516">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d6ee3-517">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-518">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-518">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-519">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d6ee3-520">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-521">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d6ee3-522">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-523">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-524">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-525">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-526">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-526">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-527">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-527">Option</span></span> | <span data-ttu-id="d6ee3-528">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-528">Default value</span></span> | <span data-ttu-id="d6ee3-529">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d6ee3-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d6ee3-531">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-532">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-532">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-533">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-534">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-535">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d6ee3-536">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-536">15 seconds</span></span> | <span data-ttu-id="d6ee3-537">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-538">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-539">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-540">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d6ee3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d6ee3-542">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-543">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-544">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-545">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d6ee3-546">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-546">Configure additional options</span></span>

<span data-ttu-id="d6ee3-547">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-548">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-549">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-549">.NET Option</span></span> |  <span data-ttu-id="d6ee3-550">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-550">Default value</span></span> | <span data-ttu-id="d6ee3-551">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-552">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d6ee3-553">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-554">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-555">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d6ee3-556">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-556">Empty</span></span> | <span data-ttu-id="d6ee3-557">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d6ee3-558">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-558">Empty</span></span> | <span data-ttu-id="d6ee3-559">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d6ee3-560">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-560">Empty</span></span> | <span data-ttu-id="d6ee3-561">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d6ee3-562">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-562">5 seconds</span></span> | <span data-ttu-id="d6ee3-563">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-563">WebSockets only.</span></span> <span data-ttu-id="d6ee3-564">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d6ee3-565">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d6ee3-566">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-566">Empty</span></span> | <span data-ttu-id="d6ee3-567">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d6ee3-568">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d6ee3-569">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="d6ee3-570">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d6ee3-571">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d6ee3-572">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="d6ee3-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d6ee3-573">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d6ee3-574">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d6ee3-575">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d6ee3-576">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d6ee3-577">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-579">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-579">JavaScript Option</span></span> | <span data-ttu-id="d6ee3-580">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-580">Default Value</span></span> | <span data-ttu-id="d6ee3-581">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d6ee3-582">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d6ee3-583">一个 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> 值，该值指定用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d6ee3-584">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d6ee3-585">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-586">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-587">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-588">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-588">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-589">Java 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-589">Java Option</span></span> | <span data-ttu-id="d6ee3-590">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-590">Default Value</span></span> | <span data-ttu-id="d6ee3-591">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-592">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d6ee3-593">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-594">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-595">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d6ee3-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d6ee3-597">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-597">Empty</span></span> | <span data-ttu-id="d6ee3-598">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d6ee3-599">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d6ee3-600">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d6ee3-601">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d6ee3-602">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6ee3-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d6ee3-603">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-604">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d6ee3-605">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d6ee3-606">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d6ee3-607">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d6ee3-608">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d6ee3-609">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d6ee3-610">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d6ee3-611">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d6ee3-612">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d6ee3-613">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d6ee3-614">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d6ee3-615">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d6ee3-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d6ee3-616">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d6ee3-617">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-617">MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-618">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d6ee3-619">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-620">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d6ee3-621">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-621">Configure server options</span></span>

<span data-ttu-id="d6ee3-622">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d6ee3-623">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-623">Option</span></span> | <span data-ttu-id="d6ee3-624">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-624">Default Value</span></span> | <span data-ttu-id="d6ee3-625">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d6ee3-626">30 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-626">30 seconds</span></span> | <span data-ttu-id="d6ee3-627">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d6ee3-628">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d6ee3-629">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-630">15 seconds</span></span> | <span data-ttu-id="d6ee3-631">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d6ee3-632">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-633">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-634">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-634">15 seconds</span></span> | <span data-ttu-id="d6ee3-635">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d6ee3-636">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d6ee3-637">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d6ee3-638">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="d6ee3-638">All installed protocols</span></span> | <span data-ttu-id="d6ee3-639">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-639">Protocols supported by this hub.</span></span> <span data-ttu-id="d6ee3-640">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d6ee3-641">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d6ee3-642">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d6ee3-643">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d6ee3-644">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d6ee3-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-645">32 KB</span></span> | <span data-ttu-id="d6ee3-646">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d6ee3-647">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d6ee3-648">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d6ee3-649">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d6ee3-650">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d6ee3-651">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d6ee3-652">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d6ee3-653">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-653">Option</span></span> | <span data-ttu-id="d6ee3-654">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-654">Default Value</span></span> | <span data-ttu-id="d6ee3-655">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d6ee3-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-656">32 KB</span></span> | <span data-ttu-id="d6ee3-657">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d6ee3-658">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d6ee3-659">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d6ee3-660">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d6ee3-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-661">32 KB</span></span> | <span data-ttu-id="d6ee3-662">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d6ee3-663">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d6ee3-664">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-664">All Transports are enabled.</span></span> | <span data-ttu-id="d6ee3-665">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d6ee3-666">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-666">See below.</span></span> | <span data-ttu-id="d6ee3-667">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d6ee3-668">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-668">See below.</span></span> | <span data-ttu-id="d6ee3-669">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d6ee3-670">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d6ee3-671">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-671">Option</span></span> | <span data-ttu-id="d6ee3-672">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-672">Default Value</span></span> | <span data-ttu-id="d6ee3-673">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d6ee3-674">90秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-674">90 seconds</span></span> | <span data-ttu-id="d6ee3-675">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d6ee3-676">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d6ee3-677">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d6ee3-678">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-678">Option</span></span> | <span data-ttu-id="d6ee3-679">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-679">Default Value</span></span> | <span data-ttu-id="d6ee3-680">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d6ee3-681">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-681">5 seconds</span></span> | <span data-ttu-id="d6ee3-682">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d6ee3-683">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d6ee3-684">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d6ee3-685">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-685">Configure client options</span></span>

<span data-ttu-id="d6ee3-686">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d6ee3-687">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d6ee3-688">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="d6ee3-688">Configure logging</span></span>

<span data-ttu-id="d6ee3-689">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d6ee3-690">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d6ee3-691">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-692">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d6ee3-693">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d6ee3-694">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d6ee3-695">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d6ee3-696">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d6ee3-697">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d6ee3-698">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d6ee3-699">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d6ee3-700">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d6ee3-701">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-701">The following table lists the available log levels.</span></span> <span data-ttu-id="d6ee3-702">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d6ee3-703">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d6ee3-704">String</span><span class="sxs-lookup"><span data-stu-id="d6ee3-704">String</span></span>                      | <span data-ttu-id="d6ee3-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d6ee3-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d6ee3-706">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d6ee3-707">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d6ee3-708">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d6ee3-709">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d6ee3-710">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d6ee3-711">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d6ee3-712">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d6ee3-713">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d6ee3-714">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d6ee3-715">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="d6ee3-715">Configure allowed transports</span></span>

<span data-ttu-id="d6ee3-716">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d6ee3-717">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d6ee3-718">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-718">All transports are enabled by default.</span></span>

<span data-ttu-id="d6ee3-719">例如，禁用 Server-Sent 事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d6ee3-720">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d6ee3-721">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d6ee3-722">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d6ee3-723">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d6ee3-724">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d6ee3-725">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="d6ee3-725">Configure bearer authentication</span></span>

<span data-ttu-id="d6ee3-726">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d6ee3-727">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d6ee3-728">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在 Server-Sent 事件和 websocket 请求)  (具体应用标头的能力。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d6ee3-729">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d6ee3-730">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d6ee3-731">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d6ee3-732">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d6ee3-733">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d6ee3-734">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d6ee3-735">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d6ee3-736">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-737">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-738">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-738">Option</span></span> | <span data-ttu-id="d6ee3-739">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-739">Default value</span></span> | <span data-ttu-id="d6ee3-740">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d6ee3-741">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-742">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-742">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-743">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-744">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-745">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-746">15 seconds</span></span> | <span data-ttu-id="d6ee3-747">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-748">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-749">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-750">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-751">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-751">15 seconds</span></span> | <span data-ttu-id="d6ee3-752">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-753">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-754">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d6ee3-755">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-757">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-757">Option</span></span> | <span data-ttu-id="d6ee3-758">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-758">Default value</span></span> | <span data-ttu-id="d6ee3-759">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d6ee3-760">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-761">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-761">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-762">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d6ee3-763">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-764">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d6ee3-765">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-766">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-767">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-768">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-769">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-769">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-770">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-770">Option</span></span> | <span data-ttu-id="d6ee3-771">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-771">Default value</span></span> | <span data-ttu-id="d6ee3-772">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d6ee3-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d6ee3-774">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-775">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-775">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-776">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-777">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-778">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d6ee3-779">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-779">15 seconds</span></span> | <span data-ttu-id="d6ee3-780">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-781">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-782">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-783">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d6ee3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d6ee3-785">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-786">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-787">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-788">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d6ee3-789">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-789">Configure additional options</span></span>

<span data-ttu-id="d6ee3-790">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-791">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-792">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-792">.NET Option</span></span> |  <span data-ttu-id="d6ee3-793">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-793">Default value</span></span> | <span data-ttu-id="d6ee3-794">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-795">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d6ee3-796">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-797">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-798">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d6ee3-799">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-799">Empty</span></span> | <span data-ttu-id="d6ee3-800">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d6ee3-801">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-801">Empty</span></span> | <span data-ttu-id="d6ee3-802">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d6ee3-803">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-803">Empty</span></span> | <span data-ttu-id="d6ee3-804">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d6ee3-805">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-805">5 seconds</span></span> | <span data-ttu-id="d6ee3-806">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-806">WebSockets only.</span></span> <span data-ttu-id="d6ee3-807">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d6ee3-808">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d6ee3-809">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-809">Empty</span></span> | <span data-ttu-id="d6ee3-810">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d6ee3-811">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d6ee3-812">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="d6ee3-813">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d6ee3-814">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d6ee3-815">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="d6ee3-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d6ee3-816">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d6ee3-817">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d6ee3-818">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d6ee3-819">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d6ee3-820">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-822">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-822">JavaScript Option</span></span> | <span data-ttu-id="d6ee3-823">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-823">Default Value</span></span> | <span data-ttu-id="d6ee3-824">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d6ee3-825">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d6ee3-826">一个 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> 值，该值指定用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d6ee3-827">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d6ee3-828">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-829">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-830">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-831">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-831">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-832">Java 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-832">Java Option</span></span> | <span data-ttu-id="d6ee3-833">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-833">Default Value</span></span> | <span data-ttu-id="d6ee3-834">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-835">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d6ee3-836">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-837">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-838">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d6ee3-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d6ee3-840">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-840">Empty</span></span> | <span data-ttu-id="d6ee3-841">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d6ee3-842">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d6ee3-843">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d6ee3-844">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d6ee3-845">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6ee3-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d6ee3-846">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-847">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d6ee3-848">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d6ee3-849">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d6ee3-850">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d6ee3-851">该对象的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d6ee3-852">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d6ee3-853">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d6ee3-854">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d6ee3-855">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d6ee3-856">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d6ee3-857">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-857">MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-858">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d6ee3-859">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-860">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d6ee3-861">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-861">Configure server options</span></span>

<span data-ttu-id="d6ee3-862">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d6ee3-863">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-863">Option</span></span> | <span data-ttu-id="d6ee3-864">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-864">Default Value</span></span> | <span data-ttu-id="d6ee3-865">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d6ee3-866">30 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-866">30 seconds</span></span> | <span data-ttu-id="d6ee3-867">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d6ee3-868">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d6ee3-869">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-870">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-870">15 seconds</span></span> | <span data-ttu-id="d6ee3-871">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d6ee3-872">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-873">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-874">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-874">15 seconds</span></span> | <span data-ttu-id="d6ee3-875">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d6ee3-876">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d6ee3-877">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d6ee3-878">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="d6ee3-878">All installed protocols</span></span> | <span data-ttu-id="d6ee3-879">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-879">Protocols supported by this hub.</span></span> <span data-ttu-id="d6ee3-880">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d6ee3-881">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d6ee3-882">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d6ee3-883">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d6ee3-884">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d6ee3-885">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d6ee3-886">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d6ee3-887">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d6ee3-888">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d6ee3-889">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-889">Option</span></span> | <span data-ttu-id="d6ee3-890">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-890">Default Value</span></span> | <span data-ttu-id="d6ee3-891">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d6ee3-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-892">32 KB</span></span> | <span data-ttu-id="d6ee3-893">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d6ee3-894">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d6ee3-895">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d6ee3-896">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d6ee3-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-897">32 KB</span></span> | <span data-ttu-id="d6ee3-898">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d6ee3-899">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d6ee3-900">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-900">All Transports are enabled.</span></span> | <span data-ttu-id="d6ee3-901">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d6ee3-902">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-902">See below.</span></span> | <span data-ttu-id="d6ee3-903">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d6ee3-904">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-904">See below.</span></span> | <span data-ttu-id="d6ee3-905">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d6ee3-906">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d6ee3-907">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-907">Option</span></span> | <span data-ttu-id="d6ee3-908">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-908">Default Value</span></span> | <span data-ttu-id="d6ee3-909">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d6ee3-910">90秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-910">90 seconds</span></span> | <span data-ttu-id="d6ee3-911">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d6ee3-912">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d6ee3-913">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d6ee3-914">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-914">Option</span></span> | <span data-ttu-id="d6ee3-915">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-915">Default Value</span></span> | <span data-ttu-id="d6ee3-916">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d6ee3-917">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-917">5 seconds</span></span> | <span data-ttu-id="d6ee3-918">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d6ee3-919">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d6ee3-920">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d6ee3-921">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-921">Configure client options</span></span>

<span data-ttu-id="d6ee3-922">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d6ee3-923">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d6ee3-924">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="d6ee3-924">Configure logging</span></span>

<span data-ttu-id="d6ee3-925">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d6ee3-926">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d6ee3-927">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-928">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d6ee3-929">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d6ee3-930">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d6ee3-931">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d6ee3-932">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d6ee3-933">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d6ee3-934">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d6ee3-935">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d6ee3-936">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d6ee3-937">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d6ee3-938">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d6ee3-939">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d6ee3-940">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d6ee3-941">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d6ee3-942">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="d6ee3-942">Configure allowed transports</span></span>

<span data-ttu-id="d6ee3-943">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d6ee3-944">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d6ee3-945">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-945">All transports are enabled by default.</span></span>

<span data-ttu-id="d6ee3-946">例如，禁用 Server-Sent 事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d6ee3-947">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d6ee3-948">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d6ee3-949">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="d6ee3-949">Configure bearer authentication</span></span>

<span data-ttu-id="d6ee3-950">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d6ee3-951">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d6ee3-952">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在 Server-Sent 事件和 websocket 请求)  (具体应用标头的能力。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d6ee3-953">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d6ee3-954">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d6ee3-955">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d6ee3-956">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d6ee3-957">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d6ee3-958">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d6ee3-959">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d6ee3-960">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-961">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-962">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-962">Option</span></span> | <span data-ttu-id="d6ee3-963">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-963">Default value</span></span> | <span data-ttu-id="d6ee3-964">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d6ee3-965">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-966">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-966">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-967">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-968">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-969">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-970">15 seconds</span></span> | <span data-ttu-id="d6ee3-971">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-972">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-973">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-974">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-975">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-975">15 seconds</span></span> | <span data-ttu-id="d6ee3-976">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-977">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-978">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d6ee3-979">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-981">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-981">Option</span></span> | <span data-ttu-id="d6ee3-982">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-982">Default value</span></span> | <span data-ttu-id="d6ee3-983">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d6ee3-984">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-985">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-985">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-986">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d6ee3-987">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-988">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d6ee3-989">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-990">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-991">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-992">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-993">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-993">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-994">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-994">Option</span></span> | <span data-ttu-id="d6ee3-995">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-995">Default value</span></span> | <span data-ttu-id="d6ee3-996">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d6ee3-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d6ee3-998">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-999">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-999">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-1000">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-1001">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-1002">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d6ee3-1003">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1003">15 seconds</span></span> | <span data-ttu-id="d6ee3-1004">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-1005">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-1006">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-1007">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d6ee3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d6ee3-1009">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-1010">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d6ee3-1011">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d6ee3-1012">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d6ee3-1013">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1013">Configure additional options</span></span>

<span data-ttu-id="d6ee3-1014">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-1016">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1016">.NET Option</span></span> |  <span data-ttu-id="d6ee3-1017">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1017">Default value</span></span> | <span data-ttu-id="d6ee3-1018">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-1019">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d6ee3-1020">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1021">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1022">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d6ee3-1023">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1023">Empty</span></span> | <span data-ttu-id="d6ee3-1024">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d6ee3-1025">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1025">Empty</span></span> | <span data-ttu-id="d6ee3-1026">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d6ee3-1027">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1027">Empty</span></span> | <span data-ttu-id="d6ee3-1028">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d6ee3-1029">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1029">5 seconds</span></span> | <span data-ttu-id="d6ee3-1030">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1030">WebSockets only.</span></span> <span data-ttu-id="d6ee3-1031">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d6ee3-1032">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d6ee3-1033">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1033">Empty</span></span> | <span data-ttu-id="d6ee3-1034">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d6ee3-1035">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d6ee3-1036">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="d6ee3-1037">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d6ee3-1038">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d6ee3-1039">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d6ee3-1040">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d6ee3-1041">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d6ee3-1042">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d6ee3-1043">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d6ee3-1044">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-1046">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1046">JavaScript Option</span></span> | <span data-ttu-id="d6ee3-1047">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1047">Default Value</span></span> | <span data-ttu-id="d6ee3-1048">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d6ee3-1049">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d6ee3-1050">一个 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> 值，该值指定用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d6ee3-1051">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d6ee3-1052">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1053">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1054">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-1055">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-1056">Java 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1056">Java Option</span></span> | <span data-ttu-id="d6ee3-1057">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1057">Default Value</span></span> | <span data-ttu-id="d6ee3-1058">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-1059">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d6ee3-1060">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1061">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1062">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d6ee3-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d6ee3-1064">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1064">Empty</span></span> | <span data-ttu-id="d6ee3-1065">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d6ee3-1066">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d6ee3-1067">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d6ee3-1068">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d6ee3-1069">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d6ee3-1070">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-1071">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d6ee3-1072">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d6ee3-1073">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d6ee3-1074">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d6ee3-1075">该对象的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d6ee3-1076">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d6ee3-1077">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d6ee3-1078">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d6ee3-1079">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d6ee3-1080">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d6ee3-1081">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1081">MessagePack serialization options</span></span>

<span data-ttu-id="d6ee3-1082">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d6ee3-1083">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-1084">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d6ee3-1085">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1085">Configure server options</span></span>

<span data-ttu-id="d6ee3-1086">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d6ee3-1087">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1087">Option</span></span> | <span data-ttu-id="d6ee3-1088">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1088">Default Value</span></span> | <span data-ttu-id="d6ee3-1089">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1090">15 seconds</span></span> | <span data-ttu-id="d6ee3-1091">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d6ee3-1092">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-1093">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d6ee3-1094">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1094">15 seconds</span></span> | <span data-ttu-id="d6ee3-1095">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d6ee3-1096">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d6ee3-1097">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d6ee3-1098">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1098">All installed protocols</span></span> | <span data-ttu-id="d6ee3-1099">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="d6ee3-1100">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d6ee3-1101">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d6ee3-1102">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d6ee3-1103">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d6ee3-1104">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d6ee3-1105">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d6ee3-1106">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d6ee3-1107">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d6ee3-1108">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d6ee3-1109">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1109">Option</span></span> | <span data-ttu-id="d6ee3-1110">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1110">Default Value</span></span> | <span data-ttu-id="d6ee3-1111">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d6ee3-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1112">32 KB</span></span> | <span data-ttu-id="d6ee3-1113">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d6ee3-1114">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d6ee3-1115">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d6ee3-1116">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d6ee3-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1117">32 KB</span></span> | <span data-ttu-id="d6ee3-1118">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d6ee3-1119">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d6ee3-1120">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1120">All Transports are enabled.</span></span> | <span data-ttu-id="d6ee3-1121">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d6ee3-1122">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1122">See below.</span></span> | <span data-ttu-id="d6ee3-1123">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d6ee3-1124">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1124">See below.</span></span> | <span data-ttu-id="d6ee3-1125">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d6ee3-1126">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d6ee3-1127">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1127">Option</span></span> | <span data-ttu-id="d6ee3-1128">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1128">Default Value</span></span> | <span data-ttu-id="d6ee3-1129">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d6ee3-1130">90秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1130">90 seconds</span></span> | <span data-ttu-id="d6ee3-1131">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d6ee3-1132">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d6ee3-1133">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d6ee3-1134">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1134">Option</span></span> | <span data-ttu-id="d6ee3-1135">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1135">Default Value</span></span> | <span data-ttu-id="d6ee3-1136">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d6ee3-1137">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1137">5 seconds</span></span> | <span data-ttu-id="d6ee3-1138">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d6ee3-1139">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d6ee3-1140">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d6ee3-1141">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1141">Configure client options</span></span>

<span data-ttu-id="d6ee3-1142">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d6ee3-1143">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d6ee3-1144">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1144">Configure logging</span></span>

<span data-ttu-id="d6ee3-1145">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d6ee3-1146">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d6ee3-1147">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d6ee3-1148">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d6ee3-1149">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d6ee3-1150">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d6ee3-1151">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d6ee3-1152">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d6ee3-1153">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d6ee3-1154">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d6ee3-1155">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d6ee3-1156">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d6ee3-1157">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d6ee3-1158">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d6ee3-1159">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d6ee3-1160">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d6ee3-1161">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d6ee3-1162">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1162">Configure allowed transports</span></span>

<span data-ttu-id="d6ee3-1163">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d6ee3-1164">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d6ee3-1165">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="d6ee3-1166">例如，禁用 Server-Sent 事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d6ee3-1167">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d6ee3-1168">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1168">Configure bearer authentication</span></span>

<span data-ttu-id="d6ee3-1169">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d6ee3-1170">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d6ee3-1171">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在 Server-Sent 事件和 websocket 请求)  (具体应用标头的能力。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d6ee3-1172">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d6ee3-1173">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d6ee3-1174">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d6ee3-1175">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d6ee3-1176">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d6ee3-1177">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d6ee3-1178">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d6ee3-1179">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-1181">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1181">Option</span></span> | <span data-ttu-id="d6ee3-1182">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1182">Default value</span></span> | <span data-ttu-id="d6ee3-1183">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d6ee3-1184">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-1185">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1185">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-1186">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-1187">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-1188">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d6ee3-1189">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1189">15 seconds</span></span> | <span data-ttu-id="d6ee3-1190">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-1191">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d6ee3-1192">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-1193">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d6ee3-1194">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-1196">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1196">Option</span></span> | <span data-ttu-id="d6ee3-1197">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1197">Default value</span></span> | <span data-ttu-id="d6ee3-1198">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d6ee3-1199">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-1200">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1200">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-1201">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d6ee3-1202">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-1203">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-1204">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-1205">选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1205">Option</span></span> | <span data-ttu-id="d6ee3-1206">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1206">Default value</span></span> | <span data-ttu-id="d6ee3-1207">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d6ee3-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d6ee3-1209">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="d6ee3-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d6ee3-1210">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1210">Timeout for server activity.</span></span> <span data-ttu-id="d6ee3-1211">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-1212">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d6ee3-1213">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d6ee3-1214">15 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1214">15 seconds</span></span> | <span data-ttu-id="d6ee3-1215">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="d6ee3-1216">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d6ee3-1217">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d6ee3-1218">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d6ee3-1219">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1219">Configure additional options</span></span>

<span data-ttu-id="d6ee3-1220">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d6ee3-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d6ee3-1222">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1222">.NET Option</span></span> |  <span data-ttu-id="d6ee3-1223">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1223">Default value</span></span> | <span data-ttu-id="d6ee3-1224">说明</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-1225">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d6ee3-1226">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1227">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1228">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d6ee3-1229">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1229">Empty</span></span> | <span data-ttu-id="d6ee3-1230">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d6ee3-1231">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1231">Empty</span></span> | <span data-ttu-id="d6ee3-1232">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d6ee3-1233">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1233">Empty</span></span> | <span data-ttu-id="d6ee3-1234">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d6ee3-1235">5 秒</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1235">5 seconds</span></span> | <span data-ttu-id="d6ee3-1236">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1236">WebSockets only.</span></span> <span data-ttu-id="d6ee3-1237">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d6ee3-1238">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d6ee3-1239">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1239">Empty</span></span> | <span data-ttu-id="d6ee3-1240">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d6ee3-1241">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d6ee3-1242">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="d6ee3-1243">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d6ee3-1244">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d6ee3-1245">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d6ee3-1246">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d6ee3-1247">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d6ee3-1248">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d6ee3-1249">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d6ee3-1250">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d6ee3-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d6ee3-1252">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1252">JavaScript Option</span></span> | <span data-ttu-id="d6ee3-1253">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1253">Default Value</span></span> | <span data-ttu-id="d6ee3-1254">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d6ee3-1255">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d6ee3-1256">一个 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> 值，该值指定用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d6ee3-1257">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d6ee3-1258">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1259">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1260">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d6ee3-1261">Java</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="d6ee3-1262">Java 选项</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1262">Java Option</span></span> | <span data-ttu-id="d6ee3-1263">默认值</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1263">Default Value</span></span> | <span data-ttu-id="d6ee3-1264">描述</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d6ee3-1265">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d6ee3-1266">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d6ee3-1267">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d6ee3-1268">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d6ee3-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d6ee3-1270">空</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1270">Empty</span></span> | <span data-ttu-id="d6ee3-1271">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d6ee3-1272">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d6ee3-1273">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d6ee3-1274">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d6ee3-1275">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6ee3-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
