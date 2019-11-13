---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963987"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="a70d3-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="a70d3-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a70d3-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a70d3-105">ASP.NET Core SignalR 支持两种用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a70d3-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a70d3-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a70d3-108">`AddJsonProtocol`[可以在 `Startup.ConfigureServices`后添加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a70d3-109">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="a70d3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a70d3-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a70d3-111">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a70d3-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="a70d3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a70d3-113">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a70d3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a70d3-114">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a70d3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a70d3-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a70d3-116">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="a70d3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a70d3-117">如果需要 `System.Text.Json`不支持的 `Newtonsoft.Json` 功能，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a70d3-118">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该方法可在 `Startup.ConfigureServices` 方法中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="a70d3-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a70d3-119">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="a70d3-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a70d3-120">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于配置自变量和返回值的序列化 `JsonSerializerSettings` 对象的 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="a70d3-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a70d3-121">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a70d3-122">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="a70d3-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a70d3-123">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a70d3-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a70d3-124">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a70d3-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a70d3-125">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a70d3-126">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-126">MessagePack serialization options</span></span>

<span data-ttu-id="a70d3-127">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a70d3-128">有关更多详细信息，请参阅[SignalR中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="a70d3-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a70d3-129">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="a70d3-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a70d3-130">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-130">Configure server options</span></span>

<span data-ttu-id="a70d3-131">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="a70d3-132">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-132">Option</span></span> | <span data-ttu-id="a70d3-133">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-133">Default Value</span></span> | <span data-ttu-id="a70d3-134">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a70d3-135">30 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-135">30 seconds</span></span> | <span data-ttu-id="a70d3-136">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a70d3-137">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a70d3-138">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="a70d3-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a70d3-139">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-139">15 seconds</span></span> | <span data-ttu-id="a70d3-140">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="a70d3-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a70d3-141">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-142">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a70d3-143">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-143">15 seconds</span></span> | <span data-ttu-id="a70d3-144">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="a70d3-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a70d3-145">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a70d3-146">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a70d3-147">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="a70d3-147">All installed protocols</span></span> | <span data-ttu-id="a70d3-148">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-148">Protocols supported by this hub.</span></span> <span data-ttu-id="a70d3-149">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a70d3-150">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="a70d3-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a70d3-151">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a70d3-152">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a70d3-153">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a70d3-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="a70d3-154">32 KB</span></span> | <span data-ttu-id="a70d3-155">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="a70d3-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="a70d3-156">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-156">Option</span></span> | <span data-ttu-id="a70d3-157">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-157">Default Value</span></span> | <span data-ttu-id="a70d3-158">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a70d3-159">30 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-159">30 seconds</span></span> | <span data-ttu-id="a70d3-160">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a70d3-161">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a70d3-162">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="a70d3-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a70d3-163">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-163">15 seconds</span></span> | <span data-ttu-id="a70d3-164">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="a70d3-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a70d3-165">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-166">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a70d3-167">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-167">15 seconds</span></span> | <span data-ttu-id="a70d3-168">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="a70d3-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a70d3-169">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a70d3-170">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a70d3-171">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="a70d3-171">All installed protocols</span></span> | <span data-ttu-id="a70d3-172">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-172">Protocols supported by this hub.</span></span> <span data-ttu-id="a70d3-173">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a70d3-174">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="a70d3-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a70d3-175">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="a70d3-176">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-176">Option</span></span> | <span data-ttu-id="a70d3-177">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-177">Default Value</span></span> | <span data-ttu-id="a70d3-178">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a70d3-179">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-179">15 seconds</span></span> | <span data-ttu-id="a70d3-180">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="a70d3-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a70d3-181">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-182">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a70d3-183">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-183">15 seconds</span></span> | <span data-ttu-id="a70d3-184">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="a70d3-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a70d3-185">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a70d3-186">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a70d3-187">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="a70d3-187">All installed protocols</span></span> | <span data-ttu-id="a70d3-188">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-188">Protocols supported by this hub.</span></span> <span data-ttu-id="a70d3-189">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="a70d3-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a70d3-190">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="a70d3-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a70d3-191">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="a70d3-192">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a70d3-193">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="a70d3-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a70d3-194">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a70d3-195">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a70d3-196">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a70d3-197">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a70d3-198">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a70d3-199">下表描述了用于配置 ASP.NET Core SignalR的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="a70d3-200">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-200">Option</span></span> | <span data-ttu-id="a70d3-201">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-201">Default Value</span></span> | <span data-ttu-id="a70d3-202">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a70d3-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="a70d3-203">32 KB</span></span> | <span data-ttu-id="a70d3-204">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a70d3-205">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="a70d3-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a70d3-206">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="a70d3-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a70d3-207">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="a70d3-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a70d3-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="a70d3-208">32 KB</span></span> | <span data-ttu-id="a70d3-209">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a70d3-210">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="a70d3-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a70d3-211">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="a70d3-211">All Transports are enabled.</span></span> | <span data-ttu-id="a70d3-212">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a70d3-213">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="a70d3-213">See below.</span></span> | <span data-ttu-id="a70d3-214">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a70d3-215">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="a70d3-215">See below.</span></span> | <span data-ttu-id="a70d3-216">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="a70d3-217">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-217">Option</span></span> | <span data-ttu-id="a70d3-218">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-218">Default Value</span></span> | <span data-ttu-id="a70d3-219">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a70d3-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="a70d3-220">32 KB</span></span> | <span data-ttu-id="a70d3-221">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a70d3-222">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="a70d3-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a70d3-223">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="a70d3-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a70d3-224">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="a70d3-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a70d3-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="a70d3-225">32 KB</span></span> | <span data-ttu-id="a70d3-226">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a70d3-227">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="a70d3-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a70d3-228">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="a70d3-228">All Transports are enabled.</span></span> | <span data-ttu-id="a70d3-229">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a70d3-230">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="a70d3-230">See below.</span></span> | <span data-ttu-id="a70d3-231">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a70d3-232">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="a70d3-232">See below.</span></span> | <span data-ttu-id="a70d3-233">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="a70d3-234">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a70d3-235">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-235">Option</span></span> | <span data-ttu-id="a70d3-236">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-236">Default Value</span></span> | <span data-ttu-id="a70d3-237">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a70d3-238">90秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-238">90 seconds</span></span> | <span data-ttu-id="a70d3-239">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a70d3-240">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="a70d3-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a70d3-241">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a70d3-242">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-242">Option</span></span> | <span data-ttu-id="a70d3-243">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-243">Default Value</span></span> | <span data-ttu-id="a70d3-244">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a70d3-245">5 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-245">5 seconds</span></span> | <span data-ttu-id="a70d3-246">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="a70d3-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a70d3-247">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a70d3-248">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a70d3-249">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-249">Configure client options</span></span>

<span data-ttu-id="a70d3-250">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a70d3-251">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="a70d3-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a70d3-252">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="a70d3-252">Configure logging</span></span>

<span data-ttu-id="a70d3-253">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="a70d3-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a70d3-254">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="a70d3-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a70d3-255">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a70d3-256">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="a70d3-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a70d3-257">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="a70d3-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a70d3-258">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a70d3-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a70d3-259">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a70d3-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a70d3-260">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="a70d3-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a70d3-261">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="a70d3-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a70d3-262">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="a70d3-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a70d3-263">您还可以提供表示日志级别名称的 `string` 值，而不是 `LogLevel` 值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a70d3-264">当你在无权访问 `LogLevel` 常量的环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="a70d3-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a70d3-265">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="a70d3-265">The following table lists the available log levels.</span></span> <span data-ttu-id="a70d3-266">你为 `configureLogging` 提供的值设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="a70d3-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a70d3-267">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="a70d3-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a70d3-268">字符串</span><span class="sxs-lookup"><span data-stu-id="a70d3-268">String</span></span>                      | <span data-ttu-id="a70d3-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a70d3-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a70d3-270">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="a70d3-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a70d3-271">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="a70d3-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a70d3-272">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="a70d3-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a70d3-273">有关日志记录的详细信息，请参阅[SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a70d3-274">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="a70d3-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a70d3-275">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="a70d3-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a70d3-276">下面的代码段演示如何将 `java.util.logging` 与 SignalR Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="a70d3-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a70d3-277">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="a70d3-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a70d3-278">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="a70d3-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a70d3-279">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="a70d3-279">Configure allowed transports</span></span>

<span data-ttu-id="a70d3-280">SignalR 使用的传输可以在 `WithUrl` 调用中配置（在 JavaScript 中为`withUrl`）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a70d3-281">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a70d3-282">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-282">All transports are enabled by default.</span></span>

<span data-ttu-id="a70d3-283">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a70d3-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a70d3-284">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="a70d3-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a70d3-285">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="a70d3-286">在 Java 客户端中，通过 `HttpHubConnectionBuilder`上的 `withTransport` 方法选择传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a70d3-287">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="a70d3-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a70d3-288">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="a70d3-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a70d3-289">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="a70d3-289">Configure bearer authentication</span></span>

<span data-ttu-id="a70d3-290">若要将身份验证数据与 SignalR 请求一起提供，请使用 `AccessTokenProvider` 选项（在 JavaScript 中为`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a70d3-291">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a70d3-292">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a70d3-293">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="a70d3-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a70d3-294">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a70d3-295">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="a70d3-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a70d3-296">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="a70d3-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a70d3-297">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a70d3-298">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="a70d3-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a70d3-299">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a70d3-300">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="a70d3-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="a70d3-301">.NET</span><span class="sxs-lookup"><span data-stu-id="a70d3-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a70d3-302">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-302">Option</span></span> | <span data-ttu-id="a70d3-303">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-303">Default value</span></span> | <span data-ttu-id="a70d3-304">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a70d3-305">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-306">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-306">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-307">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a70d3-308">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-309">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a70d3-310">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-310">15 seconds</span></span> | <span data-ttu-id="a70d3-311">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="a70d3-312">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a70d3-313">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-314">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a70d3-315">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-315">15 seconds</span></span> | <span data-ttu-id="a70d3-316">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="a70d3-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a70d3-317">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a70d3-318">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a70d3-319">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a70d3-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a70d3-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a70d3-321">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-321">Option</span></span> | <span data-ttu-id="a70d3-322">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-322">Default value</span></span> | <span data-ttu-id="a70d3-323">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a70d3-324">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-325">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-325">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-326">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a70d3-327">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-328">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a70d3-329">15秒（15000 (毫秒)）</span><span class="sxs-lookup"><span data-stu-id="a70d3-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="a70d3-330">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="a70d3-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a70d3-331">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a70d3-332">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a70d3-333">Java</span><span class="sxs-lookup"><span data-stu-id="a70d3-333">Java</span></span>](#tab/java)

| <span data-ttu-id="a70d3-334">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-334">Option</span></span> | <span data-ttu-id="a70d3-335">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-335">Default value</span></span> | <span data-ttu-id="a70d3-336">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a70d3-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a70d3-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a70d3-338">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-339">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-339">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-340">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a70d3-341">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-342">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a70d3-343">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-343">15 seconds</span></span> | <span data-ttu-id="a70d3-344">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="a70d3-345">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a70d3-346">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-347">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a70d3-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a70d3-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a70d3-349">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-350">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="a70d3-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a70d3-351">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a70d3-352">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="a70d3-353">.NET</span><span class="sxs-lookup"><span data-stu-id="a70d3-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a70d3-354">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-354">Option</span></span> | <span data-ttu-id="a70d3-355">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-355">Default value</span></span> | <span data-ttu-id="a70d3-356">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a70d3-357">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-358">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-358">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-359">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a70d3-360">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-361">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a70d3-362">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-362">15 seconds</span></span> | <span data-ttu-id="a70d3-363">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="a70d3-364">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="a70d3-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a70d3-365">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-366">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a70d3-367">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="a70d3-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a70d3-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a70d3-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a70d3-369">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-369">Option</span></span> | <span data-ttu-id="a70d3-370">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-370">Default value</span></span> | <span data-ttu-id="a70d3-371">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a70d3-372">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-373">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-373">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-374">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a70d3-375">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-376">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a70d3-377">Java</span><span class="sxs-lookup"><span data-stu-id="a70d3-377">Java</span></span>](#tab/java)

| <span data-ttu-id="a70d3-378">选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-378">Option</span></span> | <span data-ttu-id="a70d3-379">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-379">Default value</span></span> | <span data-ttu-id="a70d3-380">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a70d3-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a70d3-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a70d3-382">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="a70d3-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a70d3-383">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="a70d3-383">Timeout for server activity.</span></span> <span data-ttu-id="a70d3-384">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a70d3-385">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="a70d3-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a70d3-386">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a70d3-387">15 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-387">15 seconds</span></span> | <span data-ttu-id="a70d3-388">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="a70d3-389">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="a70d3-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a70d3-390">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="a70d3-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a70d3-391">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="a70d3-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="a70d3-392">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-392">Configure additional options</span></span>

<span data-ttu-id="a70d3-393">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a70d3-394">.NET</span><span class="sxs-lookup"><span data-stu-id="a70d3-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a70d3-395">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-395">.NET Option</span></span> |  <span data-ttu-id="a70d3-396">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-396">Default value</span></span> | <span data-ttu-id="a70d3-397">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a70d3-398">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="a70d3-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a70d3-399">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="a70d3-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a70d3-400">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="a70d3-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a70d3-401">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a70d3-402">空</span><span class="sxs-lookup"><span data-stu-id="a70d3-402">Empty</span></span> | <span data-ttu-id="a70d3-403">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="a70d3-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a70d3-404">空</span><span class="sxs-lookup"><span data-stu-id="a70d3-404">Empty</span></span> | <span data-ttu-id="a70d3-405">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="a70d3-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a70d3-406">空</span><span class="sxs-lookup"><span data-stu-id="a70d3-406">Empty</span></span> | <span data-ttu-id="a70d3-407">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="a70d3-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a70d3-408">5 秒</span><span class="sxs-lookup"><span data-stu-id="a70d3-408">5 seconds</span></span> | <span data-ttu-id="a70d3-409">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="a70d3-409">WebSockets only.</span></span> <span data-ttu-id="a70d3-410">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="a70d3-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a70d3-411">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a70d3-412">空</span><span class="sxs-lookup"><span data-stu-id="a70d3-412">Empty</span></span> | <span data-ttu-id="a70d3-413">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="a70d3-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a70d3-414">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="a70d3-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a70d3-415">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="a70d3-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="a70d3-416">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="a70d3-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a70d3-417">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="a70d3-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a70d3-418">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="a70d3-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a70d3-419">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="a70d3-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a70d3-420">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="a70d3-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a70d3-421">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="a70d3-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a70d3-422">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="a70d3-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a70d3-423">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="a70d3-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a70d3-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a70d3-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a70d3-425">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-425">JavaScript Option</span></span> | <span data-ttu-id="a70d3-426">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-426">Default Value</span></span> | <span data-ttu-id="a70d3-427">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a70d3-428">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="a70d3-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a70d3-429">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="a70d3-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a70d3-430">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="a70d3-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a70d3-431">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a70d3-432">Java</span><span class="sxs-lookup"><span data-stu-id="a70d3-432">Java</span></span>](#tab/java)

| <span data-ttu-id="a70d3-433">Java 选项</span><span class="sxs-lookup"><span data-stu-id="a70d3-433">Java Option</span></span> | <span data-ttu-id="a70d3-434">默认值</span><span class="sxs-lookup"><span data-stu-id="a70d3-434">Default Value</span></span> | <span data-ttu-id="a70d3-435">描述</span><span class="sxs-lookup"><span data-stu-id="a70d3-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a70d3-436">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="a70d3-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a70d3-437">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="a70d3-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a70d3-438">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="a70d3-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a70d3-439">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="a70d3-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a70d3-440">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a70d3-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a70d3-441">空</span><span class="sxs-lookup"><span data-stu-id="a70d3-441">Empty</span></span> | <span data-ttu-id="a70d3-442">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="a70d3-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a70d3-443">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a70d3-444">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="a70d3-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a70d3-445">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="a70d3-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a70d3-446">其他资源</span><span class="sxs-lookup"><span data-stu-id="a70d3-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
