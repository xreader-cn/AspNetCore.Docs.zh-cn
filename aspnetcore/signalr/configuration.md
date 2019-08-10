---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 475d9664c588c06bfcd816959be8a425ee01c023
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915084"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="8e531-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="8e531-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8e531-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="8e531-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8e531-105">ASP.NET Core SignalR 支持两个用于编码消息的协议:[JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8e531-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8e531-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8e531-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化, 该扩展方法可在`Startup.ConfigureServices`方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="8e531-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8e531-108">`AddJsonProtocol`方法采用`options`接收对象的委托。</span><span class="sxs-lookup"><span data-stu-id="8e531-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8e531-109">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings`对象, 该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8e531-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8e531-110">有关更多详细信息, 请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="8e531-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="8e531-111">例如, 若要将序列化程序配置为使用 "PascalCase" 属性名称, 而不是默认的 "camelCase" 名称, 请使用以下代码:</span><span class="sxs-lookup"><span data-stu-id="8e531-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="8e531-112">在 .net 客户端中, `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8e531-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8e531-113">必须导入命名空间才能解析扩展方法: `Microsoft.Extensions.DependencyInjection`</span><span class="sxs-lookup"><span data-stu-id="8e531-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8e531-114">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8e531-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8e531-115">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="8e531-115">MessagePack serialization options</span></span>

<span data-ttu-id="8e531-116">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8e531-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8e531-117">有关更多详细信息, 请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8e531-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8e531-118">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8e531-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8e531-119">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="8e531-119">Configure server options</span></span>

<span data-ttu-id="8e531-120">下表描述了用于配置 SignalR 中心的选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="8e531-121">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-121">Option</span></span> | <span data-ttu-id="8e531-122">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-122">Default Value</span></span> | <span data-ttu-id="8e531-123">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8e531-124">30 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-124">30 seconds</span></span> | <span data-ttu-id="8e531-125">如果客户端在此时间间隔内未收到消息 (包括保持活动状态), 则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8e531-126">由于实现方式的原因, 客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8e531-127">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="8e531-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8e531-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-128">15 seconds</span></span> | <span data-ttu-id="8e531-129">如果客户端在此时间间隔内未发送初始握手消息, 连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="8e531-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8e531-130">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-131">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8e531-132">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-132">15 seconds</span></span> | <span data-ttu-id="8e531-133">如果服务器未在此时间间隔内发送消息, 则会自动发送 ping 消息, 使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="8e531-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8e531-134">更改`KeepAliveInterval`时, 请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8e531-135">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="8e531-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8e531-136">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="8e531-136">All installed protocols</span></span> | <span data-ttu-id="8e531-137">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-137">Protocols supported by this hub.</span></span> <span data-ttu-id="8e531-138">默认情况下, 将允许在服务器上注册的所有协议, 但可以从此列表中删除协议, 以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8e531-139">如果`true`为, 则在集线器方法中引发异常时, 详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="8e531-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8e531-140">默认值为`false`, 因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="8e531-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="8e531-141">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="8e531-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="8e531-142">如果达到此限制, 则会阻止处理调用, 直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="8e531-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="8e531-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="8e531-143">32 KB</span></span> | <span data-ttu-id="8e531-144">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="8e531-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="8e531-145">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-145">Option</span></span> | <span data-ttu-id="8e531-146">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-146">Default Value</span></span> | <span data-ttu-id="8e531-147">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8e531-148">30 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-148">30 seconds</span></span> | <span data-ttu-id="8e531-149">如果客户端在此时间间隔内未收到消息 (包括保持活动状态), 则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8e531-150">由于实现方式的原因, 客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8e531-151">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="8e531-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8e531-152">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-152">15 seconds</span></span> | <span data-ttu-id="8e531-153">如果客户端在此时间间隔内未发送初始握手消息, 连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="8e531-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8e531-154">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-155">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8e531-156">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-156">15 seconds</span></span> | <span data-ttu-id="8e531-157">如果服务器未在此时间间隔内发送消息, 则会自动发送 ping 消息, 使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="8e531-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8e531-158">更改`KeepAliveInterval`时, 请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8e531-159">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="8e531-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8e531-160">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="8e531-160">All installed protocols</span></span> | <span data-ttu-id="8e531-161">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-161">Protocols supported by this hub.</span></span> <span data-ttu-id="8e531-162">默认情况下, 将允许在服务器上注册的所有协议, 但可以从此列表中删除协议, 以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8e531-163">如果`true`为, 则在集线器方法中引发异常时, 详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="8e531-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8e531-164">默认值为`false`, 因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="8e531-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="8e531-165">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-165">Option</span></span> | <span data-ttu-id="8e531-166">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-166">Default Value</span></span> | <span data-ttu-id="8e531-167">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="8e531-168">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-168">15 seconds</span></span> | <span data-ttu-id="8e531-169">如果客户端在此时间间隔内未发送初始握手消息, 连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="8e531-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8e531-170">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-171">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8e531-172">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-172">15 seconds</span></span> | <span data-ttu-id="8e531-173">如果服务器未在此时间间隔内发送消息, 则会自动发送 ping 消息, 使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="8e531-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8e531-174">更改`KeepAliveInterval`时, 请更改`ServerTimeout`客户端上的/ `serverTimeoutInMilliseconds`设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8e531-175">建议`ServerTimeout` 值为值/ 的`KeepAliveInterval`两倍。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="8e531-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8e531-176">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="8e531-176">All installed protocols</span></span> | <span data-ttu-id="8e531-177">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-177">Protocols supported by this hub.</span></span> <span data-ttu-id="8e531-178">默认情况下, 将允许在服务器上注册的所有协议, 但可以从此列表中删除协议, 以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="8e531-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8e531-179">如果`true`为, 则在集线器方法中引发异常时, 详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="8e531-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8e531-180">默认值为`false`, 因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="8e531-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="8e531-181">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托, 为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8e531-182">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项, 可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置:</span><span class="sxs-lookup"><span data-stu-id="8e531-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8e531-183">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="8e531-183">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8e531-184">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8e531-185">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="8e531-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8e531-186">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-186">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="8e531-187">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-187">Option</span></span> | <span data-ttu-id="8e531-188">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-188">Default Value</span></span> | <span data-ttu-id="8e531-189">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-189">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8e531-190">32 KB</span><span class="sxs-lookup"><span data-stu-id="8e531-190">32 KB</span></span> | <span data-ttu-id="8e531-191">在应用反压之前, 服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="8e531-191">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="8e531-192">增大此值后, 服务器可以更快地接收更大的消息, 而无需应用反压, 但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="8e531-192">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8e531-193">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="8e531-193">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8e531-194">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="8e531-194">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8e531-195">32 KB</span><span class="sxs-lookup"><span data-stu-id="8e531-195">32 KB</span></span> | <span data-ttu-id="8e531-196">在观察反压之前, 服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="8e531-196">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="8e531-197">增大此值后, 服务器可以更快地缓冲更大的消息, 而无需等待反压, 但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="8e531-197">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8e531-198">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="8e531-198">All Transports are enabled.</span></span> | <span data-ttu-id="8e531-199">`HttpTransportType`值的位标志枚举, 可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-199">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8e531-200">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="8e531-200">See below.</span></span> | <span data-ttu-id="8e531-201">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-201">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8e531-202">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="8e531-202">See below.</span></span> | <span data-ttu-id="8e531-203">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-203">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="8e531-204">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-204">Option</span></span> | <span data-ttu-id="8e531-205">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-205">Default Value</span></span> | <span data-ttu-id="8e531-206">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-206">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8e531-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="8e531-207">32 KB</span></span> | <span data-ttu-id="8e531-208">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="8e531-208">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="8e531-209">增大此值可使服务器接收更大的消息, 但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="8e531-209">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8e531-210">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="8e531-210">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8e531-211">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="8e531-211">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8e531-212">32 KB</span><span class="sxs-lookup"><span data-stu-id="8e531-212">32 KB</span></span> | <span data-ttu-id="8e531-213">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="8e531-213">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="8e531-214">增大此值后, 服务器将发送更大的消息, 但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="8e531-214">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8e531-215">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="8e531-215">All Transports are enabled.</span></span> | <span data-ttu-id="8e531-216">`HttpTransportType`值的位标志枚举, 可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-216">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8e531-217">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="8e531-217">See below.</span></span> | <span data-ttu-id="8e531-218">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-218">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8e531-219">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="8e531-219">See below.</span></span> | <span data-ttu-id="8e531-220">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="8e531-220">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="8e531-221">长轮询传输具有可使用`LongPolling`属性配置的其他选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-221">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8e531-222">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-222">Option</span></span> | <span data-ttu-id="8e531-223">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-223">Default Value</span></span> | <span data-ttu-id="8e531-224">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-224">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8e531-225">90秒</span><span class="sxs-lookup"><span data-stu-id="8e531-225">90 seconds</span></span> | <span data-ttu-id="8e531-226">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-226">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8e531-227">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="8e531-227">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8e531-228">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-228">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8e531-229">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-229">Option</span></span> | <span data-ttu-id="8e531-230">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-230">Default Value</span></span> | <span data-ttu-id="8e531-231">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-231">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8e531-232">5 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-232">5 seconds</span></span> | <span data-ttu-id="8e531-233">服务器关闭后, 如果客户端在此时间间隔内未能关闭, 则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="8e531-233">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8e531-234">一个委托, 可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="8e531-234">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8e531-235">委托接收客户端请求的值作为输入, 并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8e531-235">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8e531-236">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="8e531-236">Configure client options</span></span>

<span data-ttu-id="8e531-237">可以在`HubConnectionBuilder`类型上配置客户端选项 (在 .net 和 JavaScript 客户端中提供)。</span><span class="sxs-lookup"><span data-stu-id="8e531-237">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8e531-238">它在 Java 客户端中也可用, 但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容, `HubConnection`也是其本身。</span><span class="sxs-lookup"><span data-stu-id="8e531-238">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8e531-239">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="8e531-239">Configure logging</span></span>

<span data-ttu-id="8e531-240">使用`ConfigureLogging`方法在 .net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="8e531-240">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8e531-241">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="8e531-241">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8e531-242">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="8e531-242">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8e531-243">若要注册日志记录提供程序, 必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="8e531-243">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8e531-244">有关完整列表, 请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="8e531-244">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8e531-245">例如, 若要启用控制台日志记录, 请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8e531-245">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8e531-246">`AddConsole`调用扩展方法:</span><span class="sxs-lookup"><span data-stu-id="8e531-246">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8e531-247">在 JavaScript 客户端中, 存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="8e531-247">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8e531-248">提供一个`LogLevel`值, 该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="8e531-248">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8e531-249">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="8e531-249">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e531-250">您还可以`LogLevel`提供一个`string`表示日志级别名称的值, 而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="8e531-250">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="8e531-251">在无法访问常量的`LogLevel`环境中配置 SignalR 日志记录时, 这非常有用。</span><span class="sxs-lookup"><span data-stu-id="8e531-251">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="8e531-252">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="8e531-252">The following table lists the available log levels.</span></span> <span data-ttu-id="8e531-253">为`configureLogging`设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="8e531-253">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="8e531-254">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="8e531-254">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="8e531-255">String</span><span class="sxs-lookup"><span data-stu-id="8e531-255">String</span></span>                      | <span data-ttu-id="8e531-256">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8e531-256">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="8e531-257">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="8e531-257">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="8e531-258">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="8e531-258">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="8e531-259">若要完全禁用日志记录`signalR.LogLevel.None` , 请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="8e531-259">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8e531-260">有关日志记录的详细信息, 请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8e531-260">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8e531-261">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="8e531-261">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8e531-262">这是一个高级日志记录 API, 它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="8e531-262">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8e531-263">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="8e531-263">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8e531-264">如果未在依赖项中配置日志记录, SLF4J 将加载默认的非操作记录器, 并提供以下警告消息:</span><span class="sxs-lookup"><span data-stu-id="8e531-264">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8e531-265">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="8e531-265">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8e531-266">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="8e531-266">Configure allowed transports</span></span>

<span data-ttu-id="8e531-267">可以在`WithUrl`调用 (`withUrl` JavaScript) 中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-267">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8e531-268">的值`HttpTransportType`的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-268">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8e531-269">默认情况下, 将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-269">All transports are enabled by default.</span></span>

<span data-ttu-id="8e531-270">例如, 若要禁用服务器发送的事件传输, 但允许 Websocket 和长轮询连接, 请执行以下操作:</span><span class="sxs-lookup"><span data-stu-id="8e531-270">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8e531-271">在 JavaScript 客户端中, 通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输:</span><span class="sxs-lookup"><span data-stu-id="8e531-271">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8e531-272">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-272">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="8e531-273">在 Java 客户端中, 通过`withTransport`中的方法`HttpHubConnectionBuilder`选择了传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-273">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="8e531-274">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="8e531-274">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8e531-275">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="8e531-275">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8e531-276">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="8e531-276">Configure bearer authentication</span></span>

<span data-ttu-id="8e531-277">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` , 请`accessTokenFactory`使用选项 (在 JavaScript 中) 来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="8e531-277">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8e531-278">在 .net 客户端中, 此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用`Authorization`类型为的`Bearer`标头)。</span><span class="sxs-lookup"><span data-stu-id="8e531-278">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8e531-279">在 JavaScript 客户端中, 访问令牌用作持有者令牌,**但**在某些情况下, 浏览器 api 限制应用标头的能力 (具体而言, 在服务器发送事件和 websocket 请求中)。</span><span class="sxs-lookup"><span data-stu-id="8e531-279">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8e531-280">在这些情况下, 访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="8e531-280">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8e531-281">在 .net 客户端中, `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-281">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8e531-282">在 JavaScript 客户端中, 通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌:</span><span class="sxs-lookup"><span data-stu-id="8e531-282">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8e531-283">在 SignalR Java 客户端中, 可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="8e531-283">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8e531-284">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [> 的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8e531-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8e531-285">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), 你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="8e531-285">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8e531-286">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="8e531-286">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8e531-287">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身:</span><span class="sxs-lookup"><span data-stu-id="8e531-287">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="8e531-288">.NET</span><span class="sxs-lookup"><span data-stu-id="8e531-288">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8e531-289">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-289">Option</span></span> | <span data-ttu-id="8e531-290">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-290">Default value</span></span> | <span data-ttu-id="8e531-291">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8e531-292">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-293">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-293">Timeout for server activity.</span></span> <span data-ttu-id="8e531-294">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`Closed` , 并`onclose`触发事件 (在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="8e531-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8e531-295">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-296">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8e531-297">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-297">15 seconds</span></span> | <span data-ttu-id="8e531-298">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-298">Timeout for initial server handshake.</span></span> <span data-ttu-id="8e531-299">如果服务器在此时间间隔内未发送握手响应, 则客户端将取消握手, 并`Closed`触发事件`onclose` (在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="8e531-299">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8e531-300">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-300">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-301">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-301">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8e531-302">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-302">15 seconds</span></span> | <span data-ttu-id="8e531-303">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="8e531-303">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8e531-304">如果从客户端发送任何消息, 则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-304">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8e531-305">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息, 则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-305">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8e531-306">在 .net 客户端中, 超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="8e531-306">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8e531-307">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8e531-307">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8e531-308">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-308">Option</span></span> | <span data-ttu-id="8e531-309">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-309">Default value</span></span> | <span data-ttu-id="8e531-310">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-310">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8e531-311">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-311">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-312">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-312">Timeout for server activity.</span></span> <span data-ttu-id="8e531-313">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`onclose` , 并触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-313">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8e531-314">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-314">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-315">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-315">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8e531-316">15秒 (15000 (毫秒))</span><span class="sxs-lookup"><span data-stu-id="8e531-316">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="8e531-317">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="8e531-317">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8e531-318">如果从客户端发送任何消息, 则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-318">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8e531-319">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息, 则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-319">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8e531-320">Java</span><span class="sxs-lookup"><span data-stu-id="8e531-320">Java</span></span>](#tab/java)

| <span data-ttu-id="8e531-321">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-321">Option</span></span> | <span data-ttu-id="8e531-322">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-322">Default value</span></span> | <span data-ttu-id="8e531-323">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-323">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8e531-324">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8e531-324">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8e531-325">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-326">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-326">Timeout for server activity.</span></span> <span data-ttu-id="8e531-327">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`onClose` , 并触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8e531-328">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-329">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8e531-330">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-330">15 seconds</span></span> | <span data-ttu-id="8e531-331">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="8e531-332">如果服务器在此时间间隔内未发送握手响应, 则客户端将取消握手, 并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8e531-333">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-334">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-334">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8e531-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8e531-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8e531-336">15秒 (15000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-336">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8e531-337">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="8e531-337">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8e531-338">如果从客户端发送任何消息, 则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-338">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8e531-339">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息, 则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-339">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="8e531-340">.NET</span><span class="sxs-lookup"><span data-stu-id="8e531-340">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8e531-341">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-341">Option</span></span> | <span data-ttu-id="8e531-342">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-342">Default value</span></span> | <span data-ttu-id="8e531-343">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-343">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8e531-344">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-344">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-345">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-345">Timeout for server activity.</span></span> <span data-ttu-id="8e531-346">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`Closed` , 并`onclose`触发事件 (在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="8e531-346">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8e531-347">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-347">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-348">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-348">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8e531-349">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-349">15 seconds</span></span> | <span data-ttu-id="8e531-350">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-350">Timeout for initial server handshake.</span></span> <span data-ttu-id="8e531-351">如果服务器在此时间间隔内未发送握手响应, 则客户端将取消握手, 并`Closed`触发事件`onclose` (在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="8e531-351">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8e531-352">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-352">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-353">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-353">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="8e531-354">在 .net 客户端中, 超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="8e531-354">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8e531-355">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8e531-355">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8e531-356">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-356">Option</span></span> | <span data-ttu-id="8e531-357">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-357">Default value</span></span> | <span data-ttu-id="8e531-358">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-358">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8e531-359">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-359">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-360">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-360">Timeout for server activity.</span></span> <span data-ttu-id="8e531-361">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`onclose` , 并触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-361">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8e531-362">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-362">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-363">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-363">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8e531-364">Java</span><span class="sxs-lookup"><span data-stu-id="8e531-364">Java</span></span>](#tab/java)

| <span data-ttu-id="8e531-365">选项</span><span class="sxs-lookup"><span data-stu-id="8e531-365">Option</span></span> | <span data-ttu-id="8e531-366">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-366">Default value</span></span> | <span data-ttu-id="8e531-367">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-367">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8e531-368">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8e531-368">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8e531-369">30秒 (30000 毫秒)</span><span class="sxs-lookup"><span data-stu-id="8e531-369">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8e531-370">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="8e531-370">Timeout for server activity.</span></span> <span data-ttu-id="8e531-371">如果服务器未在此时间间隔内发送消息, 则客户端会将服务器视为断开连接`onClose` , 并触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-371">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8e531-372">此值必须足够大, 以便从服务器发送 ping 消息 **, 并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="8e531-372">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8e531-373">建议值至少为服务器`KeepAliveInterval`值的两倍, 以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-373">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8e531-374">15 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-374">15 seconds</span></span> | <span data-ttu-id="8e531-375">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-375">Timeout for initial server handshake.</span></span> <span data-ttu-id="8e531-376">如果服务器在此时间间隔内未发送握手响应, 则客户端将取消握手, 并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="8e531-376">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8e531-377">这是一种高级设置, 只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="8e531-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8e531-378">有关握手过程的详细信息, 请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8e531-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="8e531-379">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="8e531-379">Configure additional options</span></span>

<span data-ttu-id="8e531-380">可在上`WithUrl`的 (`withUrl` JavaScript) 方法`HubConnectionBuilder`中`HttpHubConnectionBuilder`或在 Java 客户端中的各种配置 api 上配置其他选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-380">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="8e531-381">.NET</span><span class="sxs-lookup"><span data-stu-id="8e531-381">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8e531-382">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="8e531-382">.NET Option</span></span> |  <span data-ttu-id="8e531-383">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-383">Default value</span></span> | <span data-ttu-id="8e531-384">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-384">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8e531-385">一个函数, 它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="8e531-385">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8e531-386">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="8e531-386">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8e531-387">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="8e531-387">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8e531-388">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-388">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8e531-389">空</span><span class="sxs-lookup"><span data-stu-id="8e531-389">Empty</span></span> | <span data-ttu-id="8e531-390">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="8e531-390">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8e531-391">空</span><span class="sxs-lookup"><span data-stu-id="8e531-391">Empty</span></span> | <span data-ttu-id="8e531-392">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="8e531-392">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8e531-393">空</span><span class="sxs-lookup"><span data-stu-id="8e531-393">Empty</span></span> | <span data-ttu-id="8e531-394">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="8e531-394">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8e531-395">5 秒</span><span class="sxs-lookup"><span data-stu-id="8e531-395">5 seconds</span></span> | <span data-ttu-id="8e531-396">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8e531-396">WebSockets only.</span></span> <span data-ttu-id="8e531-397">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="8e531-397">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8e531-398">如果服务器在这段时间内没有确认关闭, 客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-398">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8e531-399">空</span><span class="sxs-lookup"><span data-stu-id="8e531-399">Empty</span></span> | <span data-ttu-id="8e531-400">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="8e531-400">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8e531-401">一个委托, 可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="8e531-401">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8e531-402">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="8e531-402">Not used for WebSocket connections.</span></span> <span data-ttu-id="8e531-403">此委托必须返回非 null 值, 并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="8e531-403">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8e531-404">修改该默认值的设置并将其返回, 或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="8e531-404">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8e531-405">**当替换处理程序时, 请确保从提供的处理程序复制您要保留的设置, 否则, 配置的选项 (如 Cookie 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="8e531-405">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8e531-406">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="8e531-406">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8e531-407">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="8e531-407">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8e531-408">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="8e531-408">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8e531-409">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="8e531-409">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8e531-410">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="8e531-410">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8e531-411">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8e531-411">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8e531-412">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="8e531-412">JavaScript Option</span></span> | <span data-ttu-id="8e531-413">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-413">Default Value</span></span> | <span data-ttu-id="8e531-414">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-414">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8e531-415">一个函数, 它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="8e531-415">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8e531-416">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="8e531-416">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8e531-417">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="8e531-417">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8e531-418">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-418">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8e531-419">Java</span><span class="sxs-lookup"><span data-stu-id="8e531-419">Java</span></span>](#tab/java)

| <span data-ttu-id="8e531-420">Java 选项</span><span class="sxs-lookup"><span data-stu-id="8e531-420">Java Option</span></span> | <span data-ttu-id="8e531-421">默认值</span><span class="sxs-lookup"><span data-stu-id="8e531-421">Default Value</span></span> | <span data-ttu-id="8e531-422">描述</span><span class="sxs-lookup"><span data-stu-id="8e531-422">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8e531-423">一个函数, 它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="8e531-423">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8e531-424">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="8e531-424">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8e531-425">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="8e531-425">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8e531-426">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="8e531-426">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8e531-427">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8e531-427">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8e531-428">空</span><span class="sxs-lookup"><span data-stu-id="8e531-428">Empty</span></span> | <span data-ttu-id="8e531-429">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="8e531-429">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8e531-430">在 .NET 客户端中, 可以通过提供给的 options 委托来`WithUrl`修改这些选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-430">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8e531-431">在 JavaScript 客户端中, 可以在提供给`withUrl`的 javascript 对象中提供这些选项:</span><span class="sxs-lookup"><span data-stu-id="8e531-431">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8e531-432">在 Java 客户端中, 这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8e531-432">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8e531-433">其他资源</span><span class="sxs-lookup"><span data-stu-id="8e531-433">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
