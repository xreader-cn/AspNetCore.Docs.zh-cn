---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/29/2019
uid: signalr/configuration
ms.openlocfilehash: ce970199984cdb8333ed1fd51f744dcda2df9c61
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667604"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="ab696-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="ab696-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ab696-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="ab696-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ab696-105">ASP.NET Core SignalR 支持使用两个协议进行消息编码：[JSON](https://www.json.org/)并[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="ab696-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ab696-106">每个协议具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="ab696-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ab696-107">JSON 序列化可以配置服务器使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法，可以添加后[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)在你`Startup.ConfigureServices`方法。</span><span class="sxs-lookup"><span data-stu-id="ab696-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ab696-108">`AddJsonProtocol`方法采用一个委托，接收`options`对象。</span><span class="sxs-lookup"><span data-stu-id="ab696-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ab696-109">[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)对该对象的属性是 JSON.NET`JsonSerializerSettings`可用于配置序列化的参数和返回值的对象。</span><span class="sxs-lookup"><span data-stu-id="ab696-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ab696-110">请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="ab696-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="ab696-111">例如，若要配置的序列化程序，而不是默认的"驼峰式大小写"名称，使用"pascal 命名法"属性名称，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="ab696-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="ab696-112">在.NET 客户端，相同`AddJsonProtocol`上存在扩展方法[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="ab696-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ab696-113">`Microsoft.Extensions.DependencyInjection`必须导入命名空间，若要解决的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ab696-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ab696-114">不能在这一次配置 JavaScript 客户端中的 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="ab696-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ab696-115">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="ab696-115">MessagePack serialization options</span></span>

<span data-ttu-id="ab696-116">可以通过提供委托，配置 MessagePack 序列化[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用。</span><span class="sxs-lookup"><span data-stu-id="ab696-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ab696-117">请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="ab696-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ab696-118">不能在此时间在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="ab696-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ab696-119">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="ab696-119">Configure server options</span></span>

<span data-ttu-id="ab696-120">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="ab696-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ab696-121">选项</span><span class="sxs-lookup"><span data-stu-id="ab696-121">Option</span></span> | <span data-ttu-id="ab696-122">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-122">Default Value</span></span> | <span data-ttu-id="ab696-123">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ab696-124">30 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-124">30 seconds</span></span> | <span data-ttu-id="ab696-125">服务器将会考虑客户端断开连接，如果它未在此时间间隔内收到一条消息 （包括保持活动状态）。</span><span class="sxs-lookup"><span data-stu-id="ab696-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ab696-126">可能需要超过客户端实际上要标记为已断开连接，由于这如何实现此超时间隔。</span><span class="sxs-lookup"><span data-stu-id="ab696-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ab696-127">建议的值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="ab696-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ab696-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-128">15 seconds</span></span> | <span data-ttu-id="ab696-129">如果客户端不会在此时间间隔内发送初始握手消息，该连接已关闭。</span><span class="sxs-lookup"><span data-stu-id="ab696-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ab696-130">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="ab696-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ab696-131">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="ab696-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ab696-132">15 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-132">15 seconds</span></span> | <span data-ttu-id="ab696-133">如果服务器尚未在此时间间隔内发送一条消息，是自动发送一条 ping 消息使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="ab696-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ab696-134">更改时`KeepAliveInterval`，更改`ServerTimeout` / `serverTimeoutInMilliseconds`设置在客户端上。</span><span class="sxs-lookup"><span data-stu-id="ab696-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ab696-135">推荐`ServerTimeout` / `serverTimeoutInMilliseconds`值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="ab696-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ab696-136">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="ab696-136">All installed protocols</span></span> | <span data-ttu-id="ab696-137">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="ab696-137">Protocols supported by this hub.</span></span> <span data-ttu-id="ab696-138">默认情况下，允许在服务器上注册的所有协议，但可以从禁用特定协议的单个中心此列表中删除协议。</span><span class="sxs-lookup"><span data-stu-id="ab696-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ab696-139">如果`true`、 详细异常消息返回到客户端集线器方法中引发异常。</span><span class="sxs-lookup"><span data-stu-id="ab696-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ab696-140">默认值是`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="ab696-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ab696-141">可以通过提供一个选项委托到的所有中心配置选项`AddSignalR`调用中`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="ab696-141">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ab696-142">有关单个集线器的选项重写中提供的全局选项`AddSignalR`，可以使用配置[AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span><span class="sxs-lookup"><span data-stu-id="ab696-142">Options for a single hub override the global options provided in `AddSignalR` and can be configured using [AddHubOptions\<T>](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

<span data-ttu-id="ab696-143">使用`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级的设置。</span><span class="sxs-lookup"><span data-stu-id="ab696-143">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ab696-144">通过将传递委托，配置这些选项[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)。</span><span class="sxs-lookup"><span data-stu-id="ab696-144">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub).</span></span>

| <span data-ttu-id="ab696-145">选项</span><span class="sxs-lookup"><span data-stu-id="ab696-145">Option</span></span> | <span data-ttu-id="ab696-146">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-146">Default Value</span></span> | <span data-ttu-id="ab696-147">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ab696-148">32 KB</span><span class="sxs-lookup"><span data-stu-id="ab696-148">32 KB</span></span> | <span data-ttu-id="ab696-149">从客户端接收的字节数最大数量的服务器缓冲区。</span><span class="sxs-lookup"><span data-stu-id="ab696-149">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ab696-150">增加此值允许服务器以接收更大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="ab696-150">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ab696-151">从自动收集数据`Authorize`应用于集线器类的特性。</span><span class="sxs-lookup"><span data-stu-id="ab696-151">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ab696-152">一系列[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象用于确定客户端有权连接到中心。</span><span class="sxs-lookup"><span data-stu-id="ab696-152">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ab696-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="ab696-153">32 KB</span></span> | <span data-ttu-id="ab696-154">最大的应用发送的字节数的服务器缓冲区。</span><span class="sxs-lookup"><span data-stu-id="ab696-154">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ab696-155">增加此值允许服务器以发送较大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="ab696-155">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ab696-156">启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="ab696-156">All Transports are enabled.</span></span> | <span data-ttu-id="ab696-157">一个位掩码的`HttpTransportType`值可用于限制传输客户端可用来连接。</span><span class="sxs-lookup"><span data-stu-id="ab696-157">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ab696-158">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="ab696-158">See below.</span></span> | <span data-ttu-id="ab696-159">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="ab696-159">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ab696-160">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="ab696-160">See below.</span></span> | <span data-ttu-id="ab696-161">其他选项特定于 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="ab696-161">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ab696-162">长轮询传输中包含其他选项，可以使用配置`LongPolling`属性：</span><span class="sxs-lookup"><span data-stu-id="ab696-162">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ab696-163">选项</span><span class="sxs-lookup"><span data-stu-id="ab696-163">Option</span></span> | <span data-ttu-id="ab696-164">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-164">Default Value</span></span> | <span data-ttu-id="ab696-165">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ab696-166">90 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-166">90 seconds</span></span> | <span data-ttu-id="ab696-167">服务器等待要终止单个轮询请求之前发送到客户端的消息的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="ab696-167">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ab696-168">减小此值会导致客户端更频繁地发出新轮询请求。</span><span class="sxs-lookup"><span data-stu-id="ab696-168">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ab696-169">WebSocket 传输中包含其他选项，可以使用配置`WebSockets`属性：</span><span class="sxs-lookup"><span data-stu-id="ab696-169">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ab696-170">选项</span><span class="sxs-lookup"><span data-stu-id="ab696-170">Option</span></span> | <span data-ttu-id="ab696-171">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-171">Default Value</span></span> | <span data-ttu-id="ab696-172">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-172">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ab696-173">5 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-173">5 seconds</span></span> | <span data-ttu-id="ab696-174">在服务器关闭，如果客户端无法在此时间间隔内关闭后，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="ab696-174">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ab696-175">一个委托，它可以用来设置`Sec-WebSocket-Protocol`为自定义值的标头。</span><span class="sxs-lookup"><span data-stu-id="ab696-175">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ab696-176">该委托接收作为输入客户端请求的值，并应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="ab696-176">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ab696-177">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="ab696-177">Configure client options</span></span>

<span data-ttu-id="ab696-178">可以在配置客户端选项`HubConnectionBuilder`类型 （适用于.NET 和 JavaScript 客户端），以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="ab696-178">Client options can be configured on the `HubConnectionBuilder` type (available in both .NET and JavaScript clients), as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ab696-179">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="ab696-179">Configure logging</span></span>

<span data-ttu-id="ab696-180">使用.NET 客户端中配置日志记录`ConfigureLogging`方法。</span><span class="sxs-lookup"><span data-stu-id="ab696-180">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ab696-181">可以在相同的方式注册日志记录提供程序和筛选器，因为它们是在服务器上。</span><span class="sxs-lookup"><span data-stu-id="ab696-181">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ab696-182">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="ab696-182">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ab696-183">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="ab696-183">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ab696-184">请参阅[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分有关的完整列表。</span><span class="sxs-lookup"><span data-stu-id="ab696-184">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ab696-185">例如，若要启用控制台日志记录，安装`Microsoft.Extensions.Logging.Console`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ab696-185">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ab696-186">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ab696-186">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ab696-187">在 JavaScript 客户端，类似`configureLogging`方法存在。</span><span class="sxs-lookup"><span data-stu-id="ab696-187">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ab696-188">提供`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="ab696-188">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ab696-189">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="ab696-189">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ab696-190">若要禁用完全日志记录，请指定`signalR.LogLevel.None`在`configureLogging`方法。</span><span class="sxs-lookup"><span data-stu-id="ab696-190">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ab696-191">下面列出了可用于 JavaScript 客户端的日志级别。</span><span class="sxs-lookup"><span data-stu-id="ab696-191">Log levels available to the JavaScript client are listed below.</span></span> <span data-ttu-id="ab696-192">将日志级别设置为下列值之一，则记录上的消息**或更高版本**该级别。</span><span class="sxs-lookup"><span data-stu-id="ab696-192">Setting the log level to one of these values enables logging of messages at **or above** that level.</span></span>

| <span data-ttu-id="ab696-193">级别</span><span class="sxs-lookup"><span data-stu-id="ab696-193">Level</span></span> | <span data-ttu-id="ab696-194">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-194">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="ab696-195">未不记录任何消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-195">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="ab696-196">表示在整个应用程序时失败的消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-196">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="ab696-197">表示在当前操作失败的消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-197">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="ab696-198">表示非致命性问题的消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-198">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="ab696-199">信息性消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-199">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="ab696-200">用于调试的诊断消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-200">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="ab696-201">用于诊断特定问题非常详细的诊断消息。</span><span class="sxs-lookup"><span data-stu-id="ab696-201">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

### <a name="configure-allowed-transports"></a><span data-ttu-id="ab696-202">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="ab696-202">Configure allowed transports</span></span>

<span data-ttu-id="ab696-203">可以在中配置的 SignalR 使用的传输`WithUrl`调用 (`withUrl`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="ab696-203">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ab696-204">位 OR 运算的值`HttpTransportType`可以用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="ab696-204">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ab696-205">默认情况下启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="ab696-205">All transports are enabled by default.</span></span>

<span data-ttu-id="ab696-206">例如，若要禁用服务器发送事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="ab696-206">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ab696-207">在 JavaScript 客户端，通过设置来配置传输`transport`字段上的选项对象提供给`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ab696-207">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ab696-208">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="ab696-208">Configure bearer authentication</span></span>

<span data-ttu-id="ab696-209">若要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项 (`accessTokenFactory`在 JavaScript 中) 指定函数返回的所需的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ab696-209">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ab696-210">在.NET 客户端，此访问令牌作为传入 HTTP"持有者身份验证"令牌 (使用`Authorization`具有一种类型的标头`Bearer`)。</span><span class="sxs-lookup"><span data-stu-id="ab696-210">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ab696-211">在 JavaScript 客户端，使用访问令牌作为持有者令牌，**除**在少数情况下，在其中浏览器 Api 限制应用 （具体而言，在服务器发送事件和 Websocket 请求） 的标头的能力。</span><span class="sxs-lookup"><span data-stu-id="ab696-211">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ab696-212">在这些情况下，查询字符串值中提供的访问令牌`access_token`。</span><span class="sxs-lookup"><span data-stu-id="ab696-212">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ab696-213">在.NET 客户端，`AccessTokenProvider`可以使用中的选项委托指定选项`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="ab696-213">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ab696-214">在 JavaScript 客户端，通过设置配置的访问令牌`accessTokenFactory`字段中的选项对象`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ab696-214">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ab696-215">配置超时和保持活动状态的选项</span><span class="sxs-lookup"><span data-stu-id="ab696-215">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ab696-216">还提供用于配置超时和保持活动状态的行为的其他选项`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="ab696-216">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

| <span data-ttu-id="ab696-217">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="ab696-217">.NET Option</span></span> | <span data-ttu-id="ab696-218">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="ab696-218">JavaScript Option</span></span> | <span data-ttu-id="ab696-219">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-219">Default Value</span></span> | <span data-ttu-id="ab696-220">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-220">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `ServerTimeout` | `serverTimeoutInMilliseconds` | <span data-ttu-id="ab696-221">30 秒 （30000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="ab696-221">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ab696-222">服务器活动的超时时间。</span><span class="sxs-lookup"><span data-stu-id="ab696-222">Timeout for server activity.</span></span> <span data-ttu-id="ab696-223">如果服务器尚未在此时间间隔内发送一条消息，客户端会考虑服务器断开连接和触发器`Closed`事件 (`onclose`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="ab696-223">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ab696-224">此值必须足够大，以便从服务器发送的 ping 消息**和**超时间隔内收到的客户端。</span><span class="sxs-lookup"><span data-stu-id="ab696-224">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ab696-225">建议的值是一个数字至少两倍的服务器的`KeepAliveInterval`值，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="ab696-225">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ab696-226">不可配置</span><span class="sxs-lookup"><span data-stu-id="ab696-226">Not configurable</span></span> | <span data-ttu-id="ab696-227">15 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-227">15 seconds</span></span> | <span data-ttu-id="ab696-228">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="ab696-228">Timeout for initial server handshake.</span></span> <span data-ttu-id="ab696-229">如果服务器不在此时间间隔内发送握手响应，客户端取消握手和触发器`Closed`事件 (`onclose`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="ab696-229">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ab696-230">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="ab696-230">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ab696-231">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="ab696-231">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="ab696-232">在.NET 客户端，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="ab696-232">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span> <span data-ttu-id="ab696-233">在 JavaScript 客户端，超时值指定为一个数字，指示以毫秒为单位的持续时间。</span><span class="sxs-lookup"><span data-stu-id="ab696-233">In the JavaScript client, timeout values are specified as a number indicating the duration in milliseconds.</span></span>

### <a name="configure-additional-options"></a><span data-ttu-id="ab696-234">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="ab696-234">Configure additional options</span></span>

<span data-ttu-id="ab696-235">可以在配置其他选项`WithUrl`(`withUrl`在 JavaScript 中) 上的方法`HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ab696-235">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder`:</span></span>

| <span data-ttu-id="ab696-236">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="ab696-236">.NET Option</span></span> | <span data-ttu-id="ab696-237">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="ab696-237">JavaScript Option</span></span> | <span data-ttu-id="ab696-238">默认值</span><span class="sxs-lookup"><span data-stu-id="ab696-238">Default Value</span></span> | <span data-ttu-id="ab696-239">描述</span><span class="sxs-lookup"><span data-stu-id="ab696-239">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `AccessTokenProvider` | `accessTokenFactory` | `null` | <span data-ttu-id="ab696-240">返回一个字符串，作为持有者身份验证令牌的 HTTP 请求中提供的函数。</span><span class="sxs-lookup"><span data-stu-id="ab696-240">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `skipNegotiation` | `false` | <span data-ttu-id="ab696-241">将此设置为`true`跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="ab696-241">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ab696-242">**WebSockets 传输是唯一的已启用的传输时，才支持**。</span><span class="sxs-lookup"><span data-stu-id="ab696-242">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ab696-243">使用 Azure SignalR 服务时，不能启用此设置。</span><span class="sxs-lookup"><span data-stu-id="ab696-243">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ab696-244">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-244">Not configurable \*</span></span> | <span data-ttu-id="ab696-245">空</span><span class="sxs-lookup"><span data-stu-id="ab696-245">Empty</span></span> | <span data-ttu-id="ab696-246">若要发送请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="ab696-246">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ab696-247">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-247">Not configurable \*</span></span> | <span data-ttu-id="ab696-248">空</span><span class="sxs-lookup"><span data-stu-id="ab696-248">Empty</span></span> | <span data-ttu-id="ab696-249">要与每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="ab696-249">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ab696-250">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-250">Not configurable \*</span></span> | <span data-ttu-id="ab696-251">空</span><span class="sxs-lookup"><span data-stu-id="ab696-251">Empty</span></span> | <span data-ttu-id="ab696-252">要与每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="ab696-252">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ab696-253">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-253">Not configurable \*</span></span> | <span data-ttu-id="ab696-254">5 秒</span><span class="sxs-lookup"><span data-stu-id="ab696-254">5 seconds</span></span> | <span data-ttu-id="ab696-255">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="ab696-255">WebSockets only.</span></span> <span data-ttu-id="ab696-256">最长时间之后关闭服务器以确认关闭请求等待客户端。</span><span class="sxs-lookup"><span data-stu-id="ab696-256">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ab696-257">如果服务器不在此时间内收到结束时，客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="ab696-257">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ab696-258">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-258">Not configurable \*</span></span> | <span data-ttu-id="ab696-259">空</span><span class="sxs-lookup"><span data-stu-id="ab696-259">Empty</span></span> | <span data-ttu-id="ab696-260">附加 HTTP 标头要与每个 HTTP 请求一起发送的字典。</span><span class="sxs-lookup"><span data-stu-id="ab696-260">A dictionary of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | <span data-ttu-id="ab696-261">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-261">Not configurable \*</span></span> | `null` | <span data-ttu-id="ab696-262">一个委托，它可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="ab696-262">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ab696-263">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="ab696-263">Not used for WebSocket connections.</span></span> <span data-ttu-id="ab696-264">此委托必须返回一个非 null 值，并接收作为参数的默认值。</span><span class="sxs-lookup"><span data-stu-id="ab696-264">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ab696-265">修改该默认值上的设置，返回它，或者返回一个新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="ab696-265">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ab696-266">**时替换处理程序请务必复制你想要保留从提供的处理程序的设置，否则，配置的选项 （例如 Cookie 和标头） 不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="ab696-266">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | <span data-ttu-id="ab696-267">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-267">Not configurable \*</span></span> | `null` | <span data-ttu-id="ab696-268">发送 HTTP 请求时要使用 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="ab696-268">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | <span data-ttu-id="ab696-269">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-269">Not configurable \*</span></span> | `false` | <span data-ttu-id="ab696-270">设置此布尔值，若要发送的 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="ab696-270">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ab696-271">这使使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="ab696-271">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | <span data-ttu-id="ab696-272">不可配置 \*</span><span class="sxs-lookup"><span data-stu-id="ab696-272">Not configurable \*</span></span> | `null` | <span data-ttu-id="ab696-273">一个委托，可用于配置更多的 WebSocket 选项。</span><span class="sxs-lookup"><span data-stu-id="ab696-273">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ab696-274">接收的实例[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)可用于配置选项。</span><span class="sxs-lookup"><span data-stu-id="ab696-274">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

<span data-ttu-id="ab696-275">选项标有星号 （\*） 不是可在 JavaScript 客户端，由于浏览器 Api 中的限制中配置的。</span><span class="sxs-lookup"><span data-stu-id="ab696-275">Options marked with an asterisk (\*) aren't configurable in the JavaScript client, due to limitations in browser APIs.</span></span>

<span data-ttu-id="ab696-276">在.NET 客户端，这些选项可以修改选项委托提供给`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="ab696-276">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ab696-277">在 JavaScript 客户端，可以提供给 JavaScript 对象中提供这些选项`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ab696-277">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ab696-278">其他资源</span><span class="sxs-lookup"><span data-stu-id="ab696-278">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
