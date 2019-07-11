---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813456"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="118c2-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="118c2-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="118c2-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="118c2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="118c2-105">ASP.NET Core SignalR 支持使用两个协议进行消息编码：[JSON](https://www.json.org/)并[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="118c2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="118c2-106">每个协议具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="118c2-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="118c2-107">JSON 序列化可以配置服务器使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法，可以添加后[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)在你`Startup.ConfigureServices`方法。</span><span class="sxs-lookup"><span data-stu-id="118c2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="118c2-108">`AddJsonProtocol`方法采用一个委托，接收`options`对象。</span><span class="sxs-lookup"><span data-stu-id="118c2-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="118c2-109">[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)对该对象的属性是 JSON.NET`JsonSerializerSettings`可用于配置序列化的参数和返回值的对象。</span><span class="sxs-lookup"><span data-stu-id="118c2-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="118c2-110">请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="118c2-111">例如，若要配置的序列化程序，而不是默认的"驼峰式大小写"名称，使用"pascal 命名法"属性名称，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="118c2-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="118c2-112">在.NET 客户端，相同`AddJsonProtocol`上存在扩展方法[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="118c2-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="118c2-113">`Microsoft.Extensions.DependencyInjection`必须导入命名空间，若要解决的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="118c2-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="118c2-114">不能在这一次配置 JavaScript 客户端中的 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="118c2-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="118c2-115">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="118c2-115">MessagePack serialization options</span></span>

<span data-ttu-id="118c2-116">可以通过提供委托，配置 MessagePack 序列化[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用。</span><span class="sxs-lookup"><span data-stu-id="118c2-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="118c2-117">请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="118c2-118">不能在此时间在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="118c2-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="118c2-119">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="118c2-119">Configure server options</span></span>

<span data-ttu-id="118c2-120">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="118c2-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="118c2-121">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-121">Option</span></span> | <span data-ttu-id="118c2-122">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-122">Default Value</span></span> | <span data-ttu-id="118c2-123">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="118c2-124">30 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-124">30 seconds</span></span> | <span data-ttu-id="118c2-125">服务器将会考虑客户端断开连接，如果它未在此时间间隔内收到一条消息 （包括保持活动状态）。</span><span class="sxs-lookup"><span data-stu-id="118c2-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="118c2-126">可能需要超过客户端实际上要标记为已断开连接，由于这如何实现此超时间隔。</span><span class="sxs-lookup"><span data-stu-id="118c2-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="118c2-127">建议的值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="118c2-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-128">15 seconds</span></span> | <span data-ttu-id="118c2-129">如果客户端不会在此时间间隔内发送初始握手消息，该连接已关闭。</span><span class="sxs-lookup"><span data-stu-id="118c2-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="118c2-130">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="118c2-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="118c2-131">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="118c2-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="118c2-132">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-132">15 seconds</span></span> | <span data-ttu-id="118c2-133">如果服务器尚未在此时间间隔内发送一条消息，是自动发送一条 ping 消息使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="118c2-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="118c2-134">更改时`KeepAliveInterval`，更改`ServerTimeout` / `serverTimeoutInMilliseconds`设置在客户端上。</span><span class="sxs-lookup"><span data-stu-id="118c2-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="118c2-135">推荐`ServerTimeout` / `serverTimeoutInMilliseconds`值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="118c2-136">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="118c2-136">All installed protocols</span></span> | <span data-ttu-id="118c2-137">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-137">Protocols supported by this hub.</span></span> <span data-ttu-id="118c2-138">默认情况下，允许在服务器上注册的所有协议，但可以从禁用特定协议的单个中心此列表中删除协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="118c2-139">如果`true`、 详细异常消息返回到客户端集线器方法中引发异常。</span><span class="sxs-lookup"><span data-stu-id="118c2-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="118c2-140">默认值是`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="118c2-141">最大可为客户端缓存的项目数将上传流。</span><span class="sxs-lookup"><span data-stu-id="118c2-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="118c2-142">如果达到此限制，则调用的处理被阻止，直到服务器将处理流项。</span><span class="sxs-lookup"><span data-stu-id="118c2-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="118c2-143">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-143">Option</span></span> | <span data-ttu-id="118c2-144">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-144">Default Value</span></span> | <span data-ttu-id="118c2-145">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-145">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="118c2-146">30 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-146">30 seconds</span></span> | <span data-ttu-id="118c2-147">服务器将会考虑客户端断开连接，如果它未在此时间间隔内收到一条消息 （包括保持活动状态）。</span><span class="sxs-lookup"><span data-stu-id="118c2-147">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="118c2-148">可能需要超过客户端实际上要标记为已断开连接，由于这如何实现此超时间隔。</span><span class="sxs-lookup"><span data-stu-id="118c2-148">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="118c2-149">建议的值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-149">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="118c2-150">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-150">15 seconds</span></span> | <span data-ttu-id="118c2-151">如果客户端不会在此时间间隔内发送初始握手消息，该连接已关闭。</span><span class="sxs-lookup"><span data-stu-id="118c2-151">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="118c2-152">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="118c2-152">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="118c2-153">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="118c2-153">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="118c2-154">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-154">15 seconds</span></span> | <span data-ttu-id="118c2-155">如果服务器尚未在此时间间隔内发送一条消息，是自动发送一条 ping 消息使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="118c2-155">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="118c2-156">更改时`KeepAliveInterval`，更改`ServerTimeout` / `serverTimeoutInMilliseconds`设置在客户端上。</span><span class="sxs-lookup"><span data-stu-id="118c2-156">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="118c2-157">推荐`ServerTimeout` / `serverTimeoutInMilliseconds`值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-157">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="118c2-158">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="118c2-158">All installed protocols</span></span> | <span data-ttu-id="118c2-159">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-159">Protocols supported by this hub.</span></span> <span data-ttu-id="118c2-160">默认情况下，允许在服务器上注册的所有协议，但可以从禁用特定协议的单个中心此列表中删除协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-160">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="118c2-161">如果`true`、 详细异常消息返回到客户端集线器方法中引发异常。</span><span class="sxs-lookup"><span data-stu-id="118c2-161">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="118c2-162">默认值是`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-162">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="118c2-163">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-163">Option</span></span> | <span data-ttu-id="118c2-164">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-164">Default Value</span></span> | <span data-ttu-id="118c2-165">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="118c2-166">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-166">15 seconds</span></span> | <span data-ttu-id="118c2-167">如果客户端不会在此时间间隔内发送初始握手消息，该连接已关闭。</span><span class="sxs-lookup"><span data-stu-id="118c2-167">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="118c2-168">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="118c2-168">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="118c2-169">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="118c2-169">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="118c2-170">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-170">15 seconds</span></span> | <span data-ttu-id="118c2-171">如果服务器尚未在此时间间隔内发送一条消息，是自动发送一条 ping 消息使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="118c2-171">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="118c2-172">更改时`KeepAliveInterval`，更改`ServerTimeout` / `serverTimeoutInMilliseconds`设置在客户端上。</span><span class="sxs-lookup"><span data-stu-id="118c2-172">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="118c2-173">推荐`ServerTimeout` / `serverTimeoutInMilliseconds`值是双精度`KeepAliveInterval`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-173">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="118c2-174">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="118c2-174">All installed protocols</span></span> | <span data-ttu-id="118c2-175">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-175">Protocols supported by this hub.</span></span> <span data-ttu-id="118c2-176">默认情况下，允许在服务器上注册的所有协议，但可以从禁用特定协议的单个中心此列表中删除协议。</span><span class="sxs-lookup"><span data-stu-id="118c2-176">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="118c2-177">如果`true`、 详细异常消息返回到客户端集线器方法中引发异常。</span><span class="sxs-lookup"><span data-stu-id="118c2-177">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="118c2-178">默认值是`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-178">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="118c2-179">可以通过提供一个选项委托到的所有中心配置选项`AddSignalR`调用中`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="118c2-179">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="118c2-180">有关单个集线器的选项重写中提供的全局选项`AddSignalR`，可以使用配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="118c2-180">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="118c2-181">高级的 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="118c2-181">Advanced HTTP configuration options</span></span>

<span data-ttu-id="118c2-182">使用`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级的设置。</span><span class="sxs-lookup"><span data-stu-id="118c2-182">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="118c2-183">通过将传递委托，配置这些选项[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="118c2-183">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="118c2-184">下表描述了用于配置 ASP.NET Core SignalR 高级的 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="118c2-184">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="118c2-185">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-185">Option</span></span> | <span data-ttu-id="118c2-186">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-186">Default Value</span></span> | <span data-ttu-id="118c2-187">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-187">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="118c2-188">32 KB</span><span class="sxs-lookup"><span data-stu-id="118c2-188">32 KB</span></span> | <span data-ttu-id="118c2-189">从客户端接收的字节数最大数量的服务器缓冲区。</span><span class="sxs-lookup"><span data-stu-id="118c2-189">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="118c2-190">增加此值允许服务器以接收更大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="118c2-190">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="118c2-191">从自动收集数据`Authorize`应用于集线器类的特性。</span><span class="sxs-lookup"><span data-stu-id="118c2-191">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="118c2-192">一系列[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象用于确定客户端有权连接到中心。</span><span class="sxs-lookup"><span data-stu-id="118c2-192">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="118c2-193">32 KB</span><span class="sxs-lookup"><span data-stu-id="118c2-193">32 KB</span></span> | <span data-ttu-id="118c2-194">最大的应用发送的字节数的服务器缓冲区。</span><span class="sxs-lookup"><span data-stu-id="118c2-194">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="118c2-195">增加此值允许服务器以发送较大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="118c2-195">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="118c2-196">启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-196">All Transports are enabled.</span></span> | <span data-ttu-id="118c2-197">一个位掩码的`HttpTransportType`值可用于限制传输客户端可用来连接。</span><span class="sxs-lookup"><span data-stu-id="118c2-197">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="118c2-198">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="118c2-198">See below.</span></span> | <span data-ttu-id="118c2-199">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="118c2-199">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="118c2-200">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="118c2-200">See below.</span></span> | <span data-ttu-id="118c2-201">其他选项特定于 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-201">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="118c2-202">长轮询传输中包含其他选项，可以使用配置`LongPolling`属性：</span><span class="sxs-lookup"><span data-stu-id="118c2-202">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="118c2-203">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-203">Option</span></span> | <span data-ttu-id="118c2-204">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-204">Default Value</span></span> | <span data-ttu-id="118c2-205">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-205">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="118c2-206">90 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-206">90 seconds</span></span> | <span data-ttu-id="118c2-207">服务器等待要终止单个轮询请求之前发送到客户端的消息的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="118c2-207">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="118c2-208">减小此值会导致客户端更频繁地发出新轮询请求。</span><span class="sxs-lookup"><span data-stu-id="118c2-208">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="118c2-209">WebSocket 传输中包含其他选项，可以使用配置`WebSockets`属性：</span><span class="sxs-lookup"><span data-stu-id="118c2-209">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="118c2-210">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-210">Option</span></span> | <span data-ttu-id="118c2-211">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-211">Default Value</span></span> | <span data-ttu-id="118c2-212">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-212">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="118c2-213">5 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-213">5 seconds</span></span> | <span data-ttu-id="118c2-214">在服务器关闭，如果客户端无法在此时间间隔内关闭后，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="118c2-214">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="118c2-215">一个委托，它可以用来设置`Sec-WebSocket-Protocol`为自定义值的标头。</span><span class="sxs-lookup"><span data-stu-id="118c2-215">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="118c2-216">该委托接收作为输入客户端请求的值，并应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="118c2-216">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="118c2-217">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="118c2-217">Configure client options</span></span>

<span data-ttu-id="118c2-218">可以在配置客户端选项`HubConnectionBuilder`类型 （在.NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="118c2-218">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="118c2-219">此外，还可以在 Java 客户端，但`HttpHubConnectionBuilder`子类是包含的内容的生成器配置选项，以及上`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="118c2-219">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="118c2-220">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="118c2-220">Configure logging</span></span>

<span data-ttu-id="118c2-221">使用.NET 客户端中配置日志记录`ConfigureLogging`方法。</span><span class="sxs-lookup"><span data-stu-id="118c2-221">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="118c2-222">可以在相同的方式注册日志记录提供程序和筛选器，因为它们是在服务器上。</span><span class="sxs-lookup"><span data-stu-id="118c2-222">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="118c2-223">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="118c2-223">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="118c2-224">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="118c2-224">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="118c2-225">请参阅[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分有关的完整列表。</span><span class="sxs-lookup"><span data-stu-id="118c2-225">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="118c2-226">例如，若要启用控制台日志记录，安装`Microsoft.Extensions.Logging.Console`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="118c2-226">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="118c2-227">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="118c2-227">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="118c2-228">在 JavaScript 客户端，类似`configureLogging`方法存在。</span><span class="sxs-lookup"><span data-stu-id="118c2-228">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="118c2-229">提供`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="118c2-229">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="118c2-230">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="118c2-230">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="118c2-231">而不是`LogLevel`值，还可以提供`string`值，该值表示日志级别名称。</span><span class="sxs-lookup"><span data-stu-id="118c2-231">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="118c2-232">配置环境中的日志记录的 SignalR 你无权访问时，这是有用`LogLevel`常量。</span><span class="sxs-lookup"><span data-stu-id="118c2-232">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="118c2-233">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="118c2-233">The following table lists the available log levels.</span></span> <span data-ttu-id="118c2-234">向提供的值`configureLogging`设置**最小**日志将记录的级别。</span><span class="sxs-lookup"><span data-stu-id="118c2-234">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="118c2-235">在此级别，记录的消息**后列出表中的级别或**，将记录。</span><span class="sxs-lookup"><span data-stu-id="118c2-235">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="118c2-236">string</span><span class="sxs-lookup"><span data-stu-id="118c2-236">string</span></span> | <span data-ttu-id="118c2-237">LogLevel</span><span class="sxs-lookup"><span data-stu-id="118c2-237">LogLevel</span></span> |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| <span data-ttu-id="118c2-238">`"info"` **或** `"information"`</span><span class="sxs-lookup"><span data-stu-id="118c2-238">`"info"` **or** `"information"`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="118c2-239">`"warn"` **或** `"warning"`</span><span class="sxs-lookup"><span data-stu-id="118c2-239">`"warn"` **or** `"warning"`</span></span> | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="118c2-240">若要禁用完全日志记录，请指定`signalR.LogLevel.None`在`configureLogging`方法。</span><span class="sxs-lookup"><span data-stu-id="118c2-240">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="118c2-241">有关日志记录的详细信息，请参阅[SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="118c2-241">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="118c2-242">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)用于日志记录库。</span><span class="sxs-lookup"><span data-stu-id="118c2-242">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="118c2-243">它是库的一个高级日志记录 API，允许通过将特定的日志记录依赖项中选择其自己特定的日志记录实现的用户。</span><span class="sxs-lookup"><span data-stu-id="118c2-243">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="118c2-244">下面的代码段演示如何使用`java.util.logging`的 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="118c2-244">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="118c2-245">如果没有配置依赖项中的日志记录，SLF4J 加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="118c2-245">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="118c2-246">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="118c2-246">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="118c2-247">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="118c2-247">Configure allowed transports</span></span>

<span data-ttu-id="118c2-248">可以在中配置的 SignalR 使用的传输`WithUrl`调用 (`withUrl`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="118c2-248">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="118c2-249">位 OR 运算的值`HttpTransportType`可以用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-249">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="118c2-250">默认情况下启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-250">All transports are enabled by default.</span></span>

<span data-ttu-id="118c2-251">例如，若要禁用服务器发送事件传输，但允许 Websocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="118c2-251">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="118c2-252">在 JavaScript 客户端，通过设置来配置传输`transport`字段上的选项对象提供给`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="118c2-252">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="118c2-253">在此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-253">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="118c2-254">使用 Java 客户端，在选择了传输`withTransport`方法`HttpHubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="118c2-254">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="118c2-255">Java 客户端默认情况下使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="118c2-255">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="118c2-256">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="118c2-256">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="118c2-257">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="118c2-257">Configure bearer authentication</span></span>

<span data-ttu-id="118c2-258">若要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项 (`accessTokenFactory`在 JavaScript 中) 指定函数返回的所需的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="118c2-258">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="118c2-259">在.NET 客户端，此访问令牌作为传入 HTTP"持有者身份验证"令牌 (使用`Authorization`具有一种类型的标头`Bearer`)。</span><span class="sxs-lookup"><span data-stu-id="118c2-259">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="118c2-260">在 JavaScript 客户端，使用访问令牌作为持有者令牌，**除**在少数情况下，在其中浏览器 Api 限制应用 （具体而言，在服务器发送事件和 Websocket 请求） 的标头的能力。</span><span class="sxs-lookup"><span data-stu-id="118c2-260">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="118c2-261">在这些情况下，查询字符串值中提供的访问令牌`access_token`。</span><span class="sxs-lookup"><span data-stu-id="118c2-261">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="118c2-262">在.NET 客户端，`AccessTokenProvider`可以使用中的选项委托指定选项`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="118c2-262">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="118c2-263">在 JavaScript 客户端，通过设置配置的访问令牌`accessTokenFactory`字段中的选项对象`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="118c2-263">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="118c2-264">在 SignalR Java 客户端，您可以配置要用于身份验证通过提供到一个访问令牌工厂的持有者令牌[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)。</span><span class="sxs-lookup"><span data-stu-id="118c2-264">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="118c2-265">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="118c2-265">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="118c2-266">通过调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来为您的客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="118c2-266">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="118c2-267">配置超时和保持活动状态的选项</span><span class="sxs-lookup"><span data-stu-id="118c2-267">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="118c2-268">还提供用于配置超时和保持活动状态的行为的其他选项`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="118c2-268">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="118c2-269">.NET</span><span class="sxs-lookup"><span data-stu-id="118c2-269">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="118c2-270">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-270">Option</span></span> | <span data-ttu-id="118c2-271">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-271">Default value</span></span> | <span data-ttu-id="118c2-272">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-272">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="118c2-273">30 秒 （30000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="118c2-273">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="118c2-274">服务器活动的超时时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-274">Timeout for server activity.</span></span> <span data-ttu-id="118c2-275">如果服务器尚未在此时间间隔内发送一条消息，客户端会考虑服务器断开连接和触发器`Closed`事件 (`onclose`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="118c2-275">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="118c2-276">此值必须足够大，以便从服务器发送的 ping 消息**和**超时间隔内收到的客户端。</span><span class="sxs-lookup"><span data-stu-id="118c2-276">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="118c2-277">建议的值是一个数字至少两倍的服务器的`KeepAliveInterval`值，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-277">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="118c2-278">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-278">15 seconds</span></span> | <span data-ttu-id="118c2-279">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-279">Timeout for initial server handshake.</span></span> <span data-ttu-id="118c2-280">如果服务器不在此时间间隔内发送握手响应，客户端取消握手和触发器`Closed`事件 (`onclose`在 JavaScript 中)。</span><span class="sxs-lookup"><span data-stu-id="118c2-280">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="118c2-281">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="118c2-281">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="118c2-282">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="118c2-282">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="118c2-283">在.NET 客户端，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="118c2-283">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="118c2-284">JavaScript</span><span class="sxs-lookup"><span data-stu-id="118c2-284">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="118c2-285">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-285">Option</span></span> | <span data-ttu-id="118c2-286">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-286">Default value</span></span> | <span data-ttu-id="118c2-287">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-287">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="118c2-288">30 秒 （30000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="118c2-288">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="118c2-289">服务器活动的超时时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-289">Timeout for server activity.</span></span> <span data-ttu-id="118c2-290">如果服务器尚未在此时间间隔内发送一条消息，客户端会考虑服务器断开连接和触发器`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="118c2-290">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="118c2-291">此值必须足够大，以便从服务器发送的 ping 消息**和**超时间隔内收到的客户端。</span><span class="sxs-lookup"><span data-stu-id="118c2-291">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="118c2-292">建议的值是一个数字至少两倍的服务器的`KeepAliveInterval`值，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-292">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="118c2-293">Java</span><span class="sxs-lookup"><span data-stu-id="118c2-293">Java</span></span>](#tab/java)

| <span data-ttu-id="118c2-294">选项</span><span class="sxs-lookup"><span data-stu-id="118c2-294">Option</span></span> | <span data-ttu-id="118c2-295">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-295">Default value</span></span> | <span data-ttu-id="118c2-296">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-296">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="118c2-297">`getServerTimeout` `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="118c2-297">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="118c2-298">30 秒 （30000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="118c2-298">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="118c2-299">服务器活动的超时时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-299">Timeout for server activity.</span></span> <span data-ttu-id="118c2-300">如果服务器尚未在此时间间隔内发送一条消息，客户端会考虑服务器断开连接和触发器`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="118c2-300">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="118c2-301">此值必须足够大，以便从服务器发送的 ping 消息**和**超时间隔内收到的客户端。</span><span class="sxs-lookup"><span data-stu-id="118c2-301">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="118c2-302">建议的值是一个数字至少两倍的服务器的`KeepAliveInterval`值，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-302">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="118c2-303">15 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-303">15 seconds</span></span> | <span data-ttu-id="118c2-304">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="118c2-304">Timeout for initial server handshake.</span></span> <span data-ttu-id="118c2-305">如果服务器不在此时间间隔内发送握手响应，客户端取消握手和触发器`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="118c2-305">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="118c2-306">这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。</span><span class="sxs-lookup"><span data-stu-id="118c2-306">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="118c2-307">握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="118c2-307">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="118c2-308">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="118c2-308">Configure additional options</span></span>

<span data-ttu-id="118c2-309">可以在配置其他选项`WithUrl`(`withUrl`在 JavaScript 中) 上的方法`HubConnectionBuilder`或各种配置 Api 上`HttpHubConnectionBuilder`中的 Java 客户端：</span><span class="sxs-lookup"><span data-stu-id="118c2-309">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="118c2-310">.NET</span><span class="sxs-lookup"><span data-stu-id="118c2-310">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="118c2-311">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="118c2-311">.NET Option</span></span> |  <span data-ttu-id="118c2-312">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-312">Default value</span></span> | <span data-ttu-id="118c2-313">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-313">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="118c2-314">返回一个字符串，作为持有者身份验证令牌的 HTTP 请求中提供的函数。</span><span class="sxs-lookup"><span data-stu-id="118c2-314">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="118c2-315">将此设置为`true`跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="118c2-315">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="118c2-316">**WebSockets 传输是唯一的已启用的传输时，才支持**。</span><span class="sxs-lookup"><span data-stu-id="118c2-316">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="118c2-317">使用 Azure SignalR 服务时，不能启用此设置。</span><span class="sxs-lookup"><span data-stu-id="118c2-317">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="118c2-318">空</span><span class="sxs-lookup"><span data-stu-id="118c2-318">Empty</span></span> | <span data-ttu-id="118c2-319">若要发送请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="118c2-319">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="118c2-320">空</span><span class="sxs-lookup"><span data-stu-id="118c2-320">Empty</span></span> | <span data-ttu-id="118c2-321">要与每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="118c2-321">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="118c2-322">空</span><span class="sxs-lookup"><span data-stu-id="118c2-322">Empty</span></span> | <span data-ttu-id="118c2-323">要与每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="118c2-323">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="118c2-324">5 秒</span><span class="sxs-lookup"><span data-stu-id="118c2-324">5 seconds</span></span> | <span data-ttu-id="118c2-325">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="118c2-325">WebSockets only.</span></span> <span data-ttu-id="118c2-326">最长时间之后关闭服务器以确认关闭请求等待客户端。</span><span class="sxs-lookup"><span data-stu-id="118c2-326">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="118c2-327">如果服务器不在此时间内收到结束时，客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="118c2-327">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="118c2-328">空</span><span class="sxs-lookup"><span data-stu-id="118c2-328">Empty</span></span> | <span data-ttu-id="118c2-329">若要使用的每个 HTTP 请求发送附加 HTTP 标头映射。</span><span class="sxs-lookup"><span data-stu-id="118c2-329">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="118c2-330">一个委托，它可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="118c2-330">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="118c2-331">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="118c2-331">Not used for WebSocket connections.</span></span> <span data-ttu-id="118c2-332">此委托必须返回一个非 null 值，并接收作为参数的默认值。</span><span class="sxs-lookup"><span data-stu-id="118c2-332">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="118c2-333">修改该默认值上的设置，返回它，或者返回一个新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="118c2-333">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="118c2-334">**时替换处理程序请务必复制你想要保留从提供的处理程序的设置，否则，配置的选项 （例如 Cookie 和标头） 不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="118c2-334">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="118c2-335">发送 HTTP 请求时要使用 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="118c2-335">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="118c2-336">设置此布尔值，若要发送的 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="118c2-336">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="118c2-337">这使使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="118c2-337">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="118c2-338">一个委托，可用于配置更多的 WebSocket 选项。</span><span class="sxs-lookup"><span data-stu-id="118c2-338">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="118c2-339">接收的实例[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)可用于配置选项。</span><span class="sxs-lookup"><span data-stu-id="118c2-339">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="118c2-340">JavaScript</span><span class="sxs-lookup"><span data-stu-id="118c2-340">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="118c2-341">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="118c2-341">JavaScript Option</span></span> | <span data-ttu-id="118c2-342">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-342">Default Value</span></span> | <span data-ttu-id="118c2-343">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-343">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="118c2-344">返回一个字符串，作为持有者身份验证令牌的 HTTP 请求中提供的函数。</span><span class="sxs-lookup"><span data-stu-id="118c2-344">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="118c2-345">将此设置为`true`跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="118c2-345">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="118c2-346">**WebSockets 传输是唯一的已启用的传输时，才支持**。</span><span class="sxs-lookup"><span data-stu-id="118c2-346">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="118c2-347">使用 Azure SignalR 服务时，不能启用此设置。</span><span class="sxs-lookup"><span data-stu-id="118c2-347">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="118c2-348">Java</span><span class="sxs-lookup"><span data-stu-id="118c2-348">Java</span></span>](#tab/java)

| <span data-ttu-id="118c2-349">Java 选项</span><span class="sxs-lookup"><span data-stu-id="118c2-349">Java Option</span></span> | <span data-ttu-id="118c2-350">默认值</span><span class="sxs-lookup"><span data-stu-id="118c2-350">Default Value</span></span> | <span data-ttu-id="118c2-351">描述</span><span class="sxs-lookup"><span data-stu-id="118c2-351">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="118c2-352">返回一个字符串，作为持有者身份验证令牌的 HTTP 请求中提供的函数。</span><span class="sxs-lookup"><span data-stu-id="118c2-352">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="118c2-353">将此设置为`true`跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="118c2-353">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="118c2-354">**WebSockets 传输是唯一的已启用的传输时，才支持**。</span><span class="sxs-lookup"><span data-stu-id="118c2-354">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="118c2-355">使用 Azure SignalR 服务时，不能启用此设置。</span><span class="sxs-lookup"><span data-stu-id="118c2-355">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="118c2-356">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="118c2-356">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="118c2-357">空</span><span class="sxs-lookup"><span data-stu-id="118c2-357">Empty</span></span> | <span data-ttu-id="118c2-358">若要使用的每个 HTTP 请求发送附加 HTTP 标头映射。</span><span class="sxs-lookup"><span data-stu-id="118c2-358">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="118c2-359">在.NET 客户端，这些选项可以修改选项委托提供给`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="118c2-359">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="118c2-360">在 JavaScript 客户端，可以提供给 JavaScript 对象中提供这些选项`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="118c2-360">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="118c2-361">在 Java 客户端，这些选项可以配置的方法上`HttpHubConnectionBuilder`从返回 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="118c2-361">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="118c2-362">其他资源</span><span class="sxs-lookup"><span data-stu-id="118c2-362">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
