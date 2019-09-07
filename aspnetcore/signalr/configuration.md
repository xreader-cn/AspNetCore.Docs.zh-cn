---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773935"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="7a7d3-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="7a7d3-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7a7d3-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7a7d3-105">ASP.NET Core SignalR 支持两个用于编码消息的协议：[JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7a7d3-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7a7d3-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在`Startup.ConfigureServices`方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7a7d3-108">`AddJsonProtocol`方法采用`options`接收对象的委托。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7a7d3-109">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings`对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7a7d3-110">有关更多详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="7a7d3-111">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="7a7d3-112">在 .net 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7a7d3-113">必须导入命名空间才能解析扩展方法： `Microsoft.Extensions.DependencyInjection`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7a7d3-114">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7a7d3-115">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-115">MessagePack serialization options</span></span>

<span data-ttu-id="7a7d3-116">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7a7d3-117">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7a7d3-118">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7a7d3-119">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-119">Configure server options</span></span>

<span data-ttu-id="7a7d3-120">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="7a7d3-121">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-121">Option</span></span> | <span data-ttu-id="7a7d3-122">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-122">Default Value</span></span> | <span data-ttu-id="7a7d3-123">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7a7d3-124">30 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-124">30 seconds</span></span> | <span data-ttu-id="7a7d3-125">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7a7d3-126">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7a7d3-127">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7a7d3-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-128">15 seconds</span></span> | <span data-ttu-id="7a7d3-129">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7a7d3-130">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-131">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7a7d3-132">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-132">15 seconds</span></span> | <span data-ttu-id="7a7d3-133">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7a7d3-134">更改`KeepAliveInterval`时，请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7a7d3-135">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7a7d3-136">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="7a7d3-136">All installed protocols</span></span> | <span data-ttu-id="7a7d3-137">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-137">Protocols supported by this hub.</span></span> <span data-ttu-id="7a7d3-138">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7a7d3-139">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7a7d3-140">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7a7d3-141">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7a7d3-142">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7a7d3-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="7a7d3-143">32 KB</span></span> | <span data-ttu-id="7a7d3-144">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="7a7d3-145">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-145">Option</span></span> | <span data-ttu-id="7a7d3-146">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-146">Default Value</span></span> | <span data-ttu-id="7a7d3-147">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7a7d3-148">30 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-148">30 seconds</span></span> | <span data-ttu-id="7a7d3-149">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7a7d3-150">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7a7d3-151">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7a7d3-152">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-152">15 seconds</span></span> | <span data-ttu-id="7a7d3-153">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7a7d3-154">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-155">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7a7d3-156">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-156">15 seconds</span></span> | <span data-ttu-id="7a7d3-157">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7a7d3-158">更改`KeepAliveInterval`时，请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7a7d3-159">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7a7d3-160">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="7a7d3-160">All installed protocols</span></span> | <span data-ttu-id="7a7d3-161">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-161">Protocols supported by this hub.</span></span> <span data-ttu-id="7a7d3-162">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7a7d3-163">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7a7d3-164">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="7a7d3-165">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-165">Option</span></span> | <span data-ttu-id="7a7d3-166">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-166">Default Value</span></span> | <span data-ttu-id="7a7d3-167">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="7a7d3-168">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-168">15 seconds</span></span> | <span data-ttu-id="7a7d3-169">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7a7d3-170">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-171">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7a7d3-172">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-172">15 seconds</span></span> | <span data-ttu-id="7a7d3-173">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7a7d3-174">更改`KeepAliveInterval`时，请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7a7d3-175">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7a7d3-176">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="7a7d3-176">All installed protocols</span></span> | <span data-ttu-id="7a7d3-177">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-177">Protocols supported by this hub.</span></span> <span data-ttu-id="7a7d3-178">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7a7d3-179">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7a7d3-180">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="7a7d3-181">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7a7d3-182">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7a7d3-183">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-183">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a7d3-184">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7a7d3-185">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a7d3-186">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-186">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7a7d3-187">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-187">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

::: moniker-end

<span data-ttu-id="7a7d3-188">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-188">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="7a7d3-189">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-189">Option</span></span> | <span data-ttu-id="7a7d3-190">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-190">Default Value</span></span> | <span data-ttu-id="7a7d3-191">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-191">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7a7d3-192">32 KB</span><span class="sxs-lookup"><span data-stu-id="7a7d3-192">32 KB</span></span> | <span data-ttu-id="7a7d3-193">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-193">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7a7d3-194">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-194">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7a7d3-195">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-195">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7a7d3-196">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-196">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7a7d3-197">32 KB</span><span class="sxs-lookup"><span data-stu-id="7a7d3-197">32 KB</span></span> | <span data-ttu-id="7a7d3-198">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-198">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7a7d3-199">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-199">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7a7d3-200">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-200">All Transports are enabled.</span></span> | <span data-ttu-id="7a7d3-201">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-201">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7a7d3-202">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-202">See below.</span></span> | <span data-ttu-id="7a7d3-203">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-203">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7a7d3-204">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-204">See below.</span></span> | <span data-ttu-id="7a7d3-205">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-205">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="7a7d3-206">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-206">Option</span></span> | <span data-ttu-id="7a7d3-207">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-207">Default Value</span></span> | <span data-ttu-id="7a7d3-208">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-208">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7a7d3-209">32 KB</span><span class="sxs-lookup"><span data-stu-id="7a7d3-209">32 KB</span></span> | <span data-ttu-id="7a7d3-210">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-210">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7a7d3-211">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-211">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7a7d3-212">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-212">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7a7d3-213">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-213">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7a7d3-214">32 KB</span><span class="sxs-lookup"><span data-stu-id="7a7d3-214">32 KB</span></span> | <span data-ttu-id="7a7d3-215">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-215">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7a7d3-216">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-216">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7a7d3-217">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-217">All Transports are enabled.</span></span> | <span data-ttu-id="7a7d3-218">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-218">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7a7d3-219">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-219">See below.</span></span> | <span data-ttu-id="7a7d3-220">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-220">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7a7d3-221">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-221">See below.</span></span> | <span data-ttu-id="7a7d3-222">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-222">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="7a7d3-223">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-223">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7a7d3-224">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-224">Option</span></span> | <span data-ttu-id="7a7d3-225">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-225">Default Value</span></span> | <span data-ttu-id="7a7d3-226">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-226">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7a7d3-227">90秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-227">90 seconds</span></span> | <span data-ttu-id="7a7d3-228">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-228">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7a7d3-229">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-229">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7a7d3-230">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-230">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7a7d3-231">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-231">Option</span></span> | <span data-ttu-id="7a7d3-232">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-232">Default Value</span></span> | <span data-ttu-id="7a7d3-233">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-233">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7a7d3-234">5 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-234">5 seconds</span></span> | <span data-ttu-id="7a7d3-235">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-235">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7a7d3-236">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-236">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7a7d3-237">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-237">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7a7d3-238">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-238">Configure client options</span></span>

<span data-ttu-id="7a7d3-239">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-239">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7a7d3-240">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容， `HubConnection`也是其本身。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-240">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7a7d3-241">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="7a7d3-241">Configure logging</span></span>

<span data-ttu-id="7a7d3-242">使用`ConfigureLogging`方法在 .net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-242">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7a7d3-243">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-243">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7a7d3-244">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-244">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7a7d3-245">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-245">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7a7d3-246">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-246">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7a7d3-247">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-247">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7a7d3-248">`AddConsole`调用扩展方法：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-248">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7a7d3-249">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-249">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7a7d3-250">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-250">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7a7d3-251">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-251">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a7d3-252">您还可以`LogLevel`提供一个`string`表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-252">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7a7d3-253">在无法访问常量的`LogLevel`环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-253">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7a7d3-254">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-254">The following table lists the available log levels.</span></span> <span data-ttu-id="7a7d3-255">为`configureLogging`设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-255">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7a7d3-256">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-256">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7a7d3-257">String</span><span class="sxs-lookup"><span data-stu-id="7a7d3-257">String</span></span>                      | <span data-ttu-id="7a7d3-258">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7a7d3-258">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7a7d3-259">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-259">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7a7d3-260">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-260">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7a7d3-261">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-261">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7a7d3-262">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-262">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7a7d3-263">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-263">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7a7d3-264">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-264">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7a7d3-265">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-265">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7a7d3-266">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-266">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7a7d3-267">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-267">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7a7d3-268">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="7a7d3-268">Configure allowed transports</span></span>

<span data-ttu-id="7a7d3-269">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-269">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7a7d3-270">的值`HttpTransportType`的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-270">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7a7d3-271">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-271">All transports are enabled by default.</span></span>

<span data-ttu-id="7a7d3-272">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-272">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7a7d3-273">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-273">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7a7d3-274">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-274">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="7a7d3-275">在 Java 客户端中，通过`withTransport`中的方法`HttpHubConnectionBuilder`选择了传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-275">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7a7d3-276">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-276">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7a7d3-277">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-277">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7a7d3-278">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="7a7d3-278">Configure bearer authentication</span></span>

<span data-ttu-id="7a7d3-279">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-279">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7a7d3-280">在 .net 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用`Authorization`类型为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-280">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7a7d3-281">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-281">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7a7d3-282">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-282">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7a7d3-283">在 .net 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-283">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7a7d3-284">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-284">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7a7d3-285">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-285">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7a7d3-286">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [> 的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-286">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7a7d3-287">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-287">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7a7d3-288">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-288">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7a7d3-289">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-289">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="7a7d3-290">.NET</span><span class="sxs-lookup"><span data-stu-id="7a7d3-290">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7a7d3-291">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-291">Option</span></span> | <span data-ttu-id="7a7d3-292">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-292">Default value</span></span> | <span data-ttu-id="7a7d3-293">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-293">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7a7d3-294">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-294">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-295">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-295">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-296">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-296">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7a7d3-297">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-297">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-298">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-298">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7a7d3-299">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-299">15 seconds</span></span> | <span data-ttu-id="7a7d3-300">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-300">Timeout for initial server handshake.</span></span> <span data-ttu-id="7a7d3-301">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-301">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7a7d3-302">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-302">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-303">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-303">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7a7d3-304">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-304">15 seconds</span></span> | <span data-ttu-id="7a7d3-305">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-305">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7a7d3-306">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-306">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7a7d3-307">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-307">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7a7d3-308">在 .net 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-308">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7a7d3-309">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a7d3-309">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7a7d3-310">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-310">Option</span></span> | <span data-ttu-id="7a7d3-311">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-311">Default value</span></span> | <span data-ttu-id="7a7d3-312">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-312">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7a7d3-313">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-313">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-314">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-314">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-315">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-315">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7a7d3-316">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-316">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-317">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-317">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7a7d3-318">15秒（15000 (毫秒)）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-318">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="7a7d3-319">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-319">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7a7d3-320">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-320">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7a7d3-321">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-321">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7a7d3-322">Java</span><span class="sxs-lookup"><span data-stu-id="7a7d3-322">Java</span></span>](#tab/java)

| <span data-ttu-id="7a7d3-323">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-323">Option</span></span> | <span data-ttu-id="7a7d3-324">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-324">Default value</span></span> | <span data-ttu-id="7a7d3-325">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-325">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7a7d3-326">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-326">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7a7d3-327">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-327">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-328">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-328">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-329">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-329">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7a7d3-330">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-330">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-331">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-331">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7a7d3-332">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-332">15 seconds</span></span> | <span data-ttu-id="7a7d3-333">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-333">Timeout for initial server handshake.</span></span> <span data-ttu-id="7a7d3-334">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-334">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7a7d3-335">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-335">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-336">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-336">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7a7d3-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7a7d3-338">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-338">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-339">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-339">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7a7d3-340">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-340">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7a7d3-341">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-341">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="7a7d3-342">.NET</span><span class="sxs-lookup"><span data-stu-id="7a7d3-342">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7a7d3-343">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-343">Option</span></span> | <span data-ttu-id="7a7d3-344">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-344">Default value</span></span> | <span data-ttu-id="7a7d3-345">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-345">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7a7d3-346">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-346">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-347">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-347">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-348">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-348">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7a7d3-349">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-349">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-350">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-350">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7a7d3-351">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-351">15 seconds</span></span> | <span data-ttu-id="7a7d3-352">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-352">Timeout for initial server handshake.</span></span> <span data-ttu-id="7a7d3-353">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-353">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7a7d3-354">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-354">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-355">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-355">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="7a7d3-356">在 .net 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-356">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7a7d3-357">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a7d3-357">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7a7d3-358">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-358">Option</span></span> | <span data-ttu-id="7a7d3-359">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-359">Default value</span></span> | <span data-ttu-id="7a7d3-360">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-360">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7a7d3-361">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-361">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-362">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-362">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-363">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-363">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7a7d3-364">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-364">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-365">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-365">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7a7d3-366">Java</span><span class="sxs-lookup"><span data-stu-id="7a7d3-366">Java</span></span>](#tab/java)

| <span data-ttu-id="7a7d3-367">选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-367">Option</span></span> | <span data-ttu-id="7a7d3-368">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-368">Default value</span></span> | <span data-ttu-id="7a7d3-369">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-369">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7a7d3-370">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-370">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7a7d3-371">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="7a7d3-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7a7d3-372">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-372">Timeout for server activity.</span></span> <span data-ttu-id="7a7d3-373">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7a7d3-374">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7a7d3-375">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-375">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7a7d3-376">15 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-376">15 seconds</span></span> | <span data-ttu-id="7a7d3-377">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-377">Timeout for initial server handshake.</span></span> <span data-ttu-id="7a7d3-378">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-378">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7a7d3-379">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-379">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7a7d3-380">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-380">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="7a7d3-381">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-381">Configure additional options</span></span>

<span data-ttu-id="7a7d3-382">可在上`WithUrl`的（`withUrl` JavaScript）方法`HubConnectionBuilder`中`HttpHubConnectionBuilder`或在 Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-382">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="7a7d3-383">.NET</span><span class="sxs-lookup"><span data-stu-id="7a7d3-383">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7a7d3-384">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-384">.NET Option</span></span> |  <span data-ttu-id="7a7d3-385">默认值</span><span class="sxs-lookup"><span data-stu-id="7a7d3-385">Default value</span></span> | <span data-ttu-id="7a7d3-386">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-386">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7a7d3-387">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-387">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7a7d3-388">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-388">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7a7d3-389">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-389">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7a7d3-390">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-390">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7a7d3-391">空</span><span class="sxs-lookup"><span data-stu-id="7a7d3-391">Empty</span></span> | <span data-ttu-id="7a7d3-392">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-392">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7a7d3-393">空</span><span class="sxs-lookup"><span data-stu-id="7a7d3-393">Empty</span></span> | <span data-ttu-id="7a7d3-394">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-394">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7a7d3-395">空</span><span class="sxs-lookup"><span data-stu-id="7a7d3-395">Empty</span></span> | <span data-ttu-id="7a7d3-396">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-396">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7a7d3-397">5 秒</span><span class="sxs-lookup"><span data-stu-id="7a7d3-397">5 seconds</span></span> | <span data-ttu-id="7a7d3-398">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-398">WebSockets only.</span></span> <span data-ttu-id="7a7d3-399">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-399">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7a7d3-400">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-400">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7a7d3-401">空</span><span class="sxs-lookup"><span data-stu-id="7a7d3-401">Empty</span></span> | <span data-ttu-id="7a7d3-402">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-402">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7a7d3-403">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-403">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7a7d3-404">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-404">Not used for WebSocket connections.</span></span> <span data-ttu-id="7a7d3-405">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-405">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7a7d3-406">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-406">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7a7d3-407">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="7a7d3-407">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7a7d3-408">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-408">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7a7d3-409">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-409">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7a7d3-410">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-410">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7a7d3-411">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-411">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7a7d3-412">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-412">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7a7d3-413">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a7d3-413">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7a7d3-414">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-414">JavaScript Option</span></span> | <span data-ttu-id="7a7d3-415">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-415">Default Value</span></span> | <span data-ttu-id="7a7d3-416">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-416">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7a7d3-417">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-417">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7a7d3-418">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-418">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7a7d3-419">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-419">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7a7d3-420">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-420">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7a7d3-421">Java</span><span class="sxs-lookup"><span data-stu-id="7a7d3-421">Java</span></span>](#tab/java)

| <span data-ttu-id="7a7d3-422">Java 选项</span><span class="sxs-lookup"><span data-stu-id="7a7d3-422">Java Option</span></span> | <span data-ttu-id="7a7d3-423">Default Value</span><span class="sxs-lookup"><span data-stu-id="7a7d3-423">Default Value</span></span> | <span data-ttu-id="7a7d3-424">描述</span><span class="sxs-lookup"><span data-stu-id="7a7d3-424">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7a7d3-425">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-425">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7a7d3-426">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-426">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7a7d3-427">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-427">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7a7d3-428">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-428">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7a7d3-429">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-429">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7a7d3-430">空</span><span class="sxs-lookup"><span data-stu-id="7a7d3-430">Empty</span></span> | <span data-ttu-id="7a7d3-431">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="7a7d3-431">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7a7d3-432">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-432">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7a7d3-433">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="7a7d3-433">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7a7d3-434">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7a7d3-434">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7a7d3-435">其他资源</span><span class="sxs-lookup"><span data-stu-id="7a7d3-435">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
