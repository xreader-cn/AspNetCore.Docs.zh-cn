---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246497"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="173df-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="173df-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="173df-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="173df-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="173df-105">ASP.NET Core SignalR 支持两个用于编码消息的协议：[JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="173df-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="173df-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="173df-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="173df-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="173df-108">@no__t 在 AddSignalR 中的[](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后，可以添加 `AddJsonProtocol`。</span><span class="sxs-lookup"><span data-stu-id="173df-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="173df-109">@No__t-0 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="173df-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="173df-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` @no__t 2 对象，可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="173df-111">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="173df-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="173df-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请在 `Startup.ConfigureServices` 中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="173df-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="173df-113">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="173df-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="173df-114">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间以解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="173df-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="173df-115">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="173df-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="173df-116">如果你需要 `Newtonsoft.Json` 的功能，但在 `System.Text.Json` 中不受支持，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="173df-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="173df-117">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) @no__t 方法中添加之后。</span><span class="sxs-lookup"><span data-stu-id="173df-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="173df-118">@No__t-0 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="173df-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="173df-119">该对象上的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="173df-120">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="173df-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="173df-121">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices` 中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="173df-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="173df-122">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="173df-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="173df-123">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间以解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="173df-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

::: moniker-end

> [!NOTE]
> <span data-ttu-id="173df-124">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="173df-125">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="173df-125">MessagePack serialization options</span></span>

<span data-ttu-id="173df-126">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="173df-127">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="173df-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="173df-128">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="173df-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="173df-129">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="173df-129">Configure server options</span></span>

<span data-ttu-id="173df-130">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="173df-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="173df-131">选项</span><span class="sxs-lookup"><span data-stu-id="173df-131">Option</span></span> | <span data-ttu-id="173df-132">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-132">Default Value</span></span> | <span data-ttu-id="173df-133">描述</span><span class="sxs-lookup"><span data-stu-id="173df-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="173df-134">30 秒</span><span class="sxs-lookup"><span data-stu-id="173df-134">30 seconds</span></span> | <span data-ttu-id="173df-135">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="173df-136">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="173df-137">建议值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="173df-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="173df-138">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-138">15 seconds</span></span> | <span data-ttu-id="173df-139">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="173df-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="173df-140">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-141">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="173df-142">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-142">15 seconds</span></span> | <span data-ttu-id="173df-143">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="173df-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="173df-144">更改 `KeepAliveInterval` 时，请更改客户端上的 @no__t no__t-2 @ no__t 设置。</span><span class="sxs-lookup"><span data-stu-id="173df-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="173df-145">建议 `ServerTimeout` @ no__t-1 @ no__t-2 值是 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="173df-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="173df-146">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="173df-146">All installed protocols</span></span> | <span data-ttu-id="173df-147">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="173df-147">Protocols supported by this hub.</span></span> <span data-ttu-id="173df-148">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="173df-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="173df-149">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="173df-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="173df-150">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="173df-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="173df-151">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="173df-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="173df-152">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="173df-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="173df-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="173df-153">32 KB</span></span> | <span data-ttu-id="173df-154">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="173df-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="173df-155">选项</span><span class="sxs-lookup"><span data-stu-id="173df-155">Option</span></span> | <span data-ttu-id="173df-156">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-156">Default Value</span></span> | <span data-ttu-id="173df-157">描述</span><span class="sxs-lookup"><span data-stu-id="173df-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="173df-158">30 秒</span><span class="sxs-lookup"><span data-stu-id="173df-158">30 seconds</span></span> | <span data-ttu-id="173df-159">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="173df-160">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="173df-161">建议值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="173df-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="173df-162">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-162">15 seconds</span></span> | <span data-ttu-id="173df-163">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="173df-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="173df-164">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-165">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="173df-166">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-166">15 seconds</span></span> | <span data-ttu-id="173df-167">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="173df-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="173df-168">更改 `KeepAliveInterval` 时，请更改客户端上的 @no__t no__t-2 @ no__t 设置。</span><span class="sxs-lookup"><span data-stu-id="173df-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="173df-169">建议 `ServerTimeout` @ no__t-1 @ no__t-2 值是 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="173df-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="173df-170">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="173df-170">All installed protocols</span></span> | <span data-ttu-id="173df-171">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="173df-171">Protocols supported by this hub.</span></span> <span data-ttu-id="173df-172">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="173df-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="173df-173">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="173df-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="173df-174">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="173df-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="173df-175">选项</span><span class="sxs-lookup"><span data-stu-id="173df-175">Option</span></span> | <span data-ttu-id="173df-176">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-176">Default Value</span></span> | <span data-ttu-id="173df-177">描述</span><span class="sxs-lookup"><span data-stu-id="173df-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="173df-178">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-178">15 seconds</span></span> | <span data-ttu-id="173df-179">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="173df-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="173df-180">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-181">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="173df-182">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-182">15 seconds</span></span> | <span data-ttu-id="173df-183">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="173df-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="173df-184">更改 `KeepAliveInterval` 时，请更改客户端上的 @no__t no__t-2 @ no__t 设置。</span><span class="sxs-lookup"><span data-stu-id="173df-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="173df-185">建议 `ServerTimeout` @ no__t-1 @ no__t-2 值是 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="173df-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="173df-186">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="173df-186">All installed protocols</span></span> | <span data-ttu-id="173df-187">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="173df-187">Protocols supported by this hub.</span></span> <span data-ttu-id="173df-188">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="173df-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="173df-189">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="173df-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="173df-190">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="173df-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="173df-191">可以为所有中心配置选项，方法是在 `Startup.ConfigureServices` 中提供选项委托到 `AddSignalR` 调用。</span><span class="sxs-lookup"><span data-stu-id="173df-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="173df-192">单个集线器的选项会替代 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> 进行配置：</span><span class="sxs-lookup"><span data-stu-id="173df-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="173df-193">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="173df-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="173df-194">使用 @no__t 来配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="173df-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="173df-195">可以通过将委托传递到 `Startup.Configure` 中的[MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="173df-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="173df-196">使用 @no__t 来配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="173df-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="173df-197">可以通过将委托传递到 `Startup.Configure` 中的[MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="173df-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="173df-198">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="173df-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="173df-199">选项</span><span class="sxs-lookup"><span data-stu-id="173df-199">Option</span></span> | <span data-ttu-id="173df-200">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-200">Default Value</span></span> | <span data-ttu-id="173df-201">描述</span><span class="sxs-lookup"><span data-stu-id="173df-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="173df-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="173df-202">32 KB</span></span> | <span data-ttu-id="173df-203">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="173df-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="173df-204">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="173df-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="173df-205">从应用于 Hub 类的 `Authorize` 特性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="173df-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="173df-206">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="173df-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="173df-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="173df-207">32 KB</span></span> | <span data-ttu-id="173df-208">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="173df-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="173df-209">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="173df-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="173df-210">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="173df-210">All Transports are enabled.</span></span> | <span data-ttu-id="173df-211">@No__t 值为的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="173df-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="173df-212">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="173df-212">See below.</span></span> | <span data-ttu-id="173df-213">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="173df-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="173df-214">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="173df-214">See below.</span></span> | <span data-ttu-id="173df-215">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="173df-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="173df-216">选项</span><span class="sxs-lookup"><span data-stu-id="173df-216">Option</span></span> | <span data-ttu-id="173df-217">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-217">Default Value</span></span> | <span data-ttu-id="173df-218">描述</span><span class="sxs-lookup"><span data-stu-id="173df-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="173df-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="173df-219">32 KB</span></span> | <span data-ttu-id="173df-220">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="173df-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="173df-221">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="173df-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="173df-222">从应用于 Hub 类的 `Authorize` 特性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="173df-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="173df-223">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="173df-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="173df-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="173df-224">32 KB</span></span> | <span data-ttu-id="173df-225">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="173df-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="173df-226">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="173df-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="173df-227">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="173df-227">All Transports are enabled.</span></span> | <span data-ttu-id="173df-228">@No__t 值为的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="173df-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="173df-229">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="173df-229">See below.</span></span> | <span data-ttu-id="173df-230">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="173df-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="173df-231">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="173df-231">See below.</span></span> | <span data-ttu-id="173df-232">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="173df-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="173df-233">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="173df-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="173df-234">选项</span><span class="sxs-lookup"><span data-stu-id="173df-234">Option</span></span> | <span data-ttu-id="173df-235">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-235">Default Value</span></span> | <span data-ttu-id="173df-236">描述</span><span class="sxs-lookup"><span data-stu-id="173df-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="173df-237">90秒</span><span class="sxs-lookup"><span data-stu-id="173df-237">90 seconds</span></span> | <span data-ttu-id="173df-238">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="173df-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="173df-239">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="173df-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="173df-240">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="173df-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="173df-241">选项</span><span class="sxs-lookup"><span data-stu-id="173df-241">Option</span></span> | <span data-ttu-id="173df-242">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-242">Default Value</span></span> | <span data-ttu-id="173df-243">描述</span><span class="sxs-lookup"><span data-stu-id="173df-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="173df-244">5 秒</span><span class="sxs-lookup"><span data-stu-id="173df-244">5 seconds</span></span> | <span data-ttu-id="173df-245">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="173df-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="173df-246">一个委托，可用于将 @no__t 0 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="173df-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="173df-247">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="173df-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="173df-248">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="173df-248">Configure client options</span></span>

<span data-ttu-id="173df-249">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="173df-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="173df-250">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="173df-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="173df-251">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="173df-251">Configure logging</span></span>

<span data-ttu-id="173df-252">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="173df-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="173df-253">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="173df-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="173df-254">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="173df-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="173df-255">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="173df-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="173df-256">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="173df-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="173df-257">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="173df-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="173df-258">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="173df-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="173df-259">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="173df-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="173df-260">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="173df-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="173df-261">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="173df-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="173df-262">你还可以提供表示日志级别名称的 @no__t 1 值，而不是 @no__t 值。</span><span class="sxs-lookup"><span data-stu-id="173df-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="173df-263">在无法访问 `LogLevel` 常量的环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="173df-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="173df-264">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="173df-264">The following table lists the available log levels.</span></span> <span data-ttu-id="173df-265">为 @no__t 提供的值将设置要记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="173df-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="173df-266">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="173df-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="173df-267">String</span><span class="sxs-lookup"><span data-stu-id="173df-267">String</span></span>                      | <span data-ttu-id="173df-268">LogLevel</span><span class="sxs-lookup"><span data-stu-id="173df-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="173df-269">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="173df-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="173df-270">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="173df-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="173df-271">若要完全禁用日志记录，请在 @no__t 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="173df-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="173df-272">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="173df-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="173df-273">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="173df-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="173df-274">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="173df-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="173df-275">以下代码片段演示了如何在 SignalR Java 客户端上使用 @no__t 0。</span><span class="sxs-lookup"><span data-stu-id="173df-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="173df-276">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="173df-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="173df-277">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="173df-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="173df-278">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="173df-278">Configure allowed transports</span></span>

<span data-ttu-id="173df-279">可以在 `WithUrl` 调用中配置 SignalR 使用的传输（JavaScript 中的 `withUrl`）。</span><span class="sxs-lookup"><span data-stu-id="173df-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="173df-280">@No__t-0 的值的按位 OR 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="173df-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="173df-281">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="173df-281">All transports are enabled by default.</span></span>

<span data-ttu-id="173df-282">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="173df-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="173df-283">在 JavaScript 客户端中，通过在提供给 `withUrl` 的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="173df-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="173df-284">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="173df-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="173df-285">在 Java 客户端中，为 `HttpHubConnectionBuilder` 上的 `withTransport` 方法选择了传输。</span><span class="sxs-lookup"><span data-stu-id="173df-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="173df-286">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="173df-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="173df-287">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="173df-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="173df-288">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="173df-288">Configure bearer authentication</span></span>

<span data-ttu-id="173df-289">若要随 SignalR 请求一起提供身份验证数据，请使用 `AccessTokenProvider` 选项（JavaScript 中的 `accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="173df-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="173df-290">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 @no__t 为-1 的 @no__t 的标头）。</span><span class="sxs-lookup"><span data-stu-id="173df-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="173df-291">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="173df-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="173df-292">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="173df-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="173df-293">在 .NET 客户端中，可以使用 `WithUrl` 中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="173df-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="173df-294">在 JavaScript 客户端中，通过在 `withUrl` 中的 options 对象上设置 @no__t 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="173df-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="173df-295">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="173df-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="173df-296">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供 RxJava 的[](https://github.com/ReactiveX/RxJava) [单个 @ no__t-3String >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="173df-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="173df-297">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="173df-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="173df-298">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="173df-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="173df-299">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="173df-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="173df-300">.NET</span><span class="sxs-lookup"><span data-stu-id="173df-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="173df-301">选项</span><span class="sxs-lookup"><span data-stu-id="173df-301">Option</span></span> | <span data-ttu-id="173df-302">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-302">Default value</span></span> | <span data-ttu-id="173df-303">描述</span><span class="sxs-lookup"><span data-stu-id="173df-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="173df-304">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-305">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-305">Timeout for server activity.</span></span> <span data-ttu-id="173df-306">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为已断开连接，并触发 `Closed` 事件（JavaScript 中 `onclose`）。</span><span class="sxs-lookup"><span data-stu-id="173df-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="173df-307">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-308">建议的值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="173df-309">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-309">15 seconds</span></span> | <span data-ttu-id="173df-310">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="173df-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="173df-311">如果服务器未在此时间间隔内发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中 `onclose`）。</span><span class="sxs-lookup"><span data-stu-id="173df-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="173df-312">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-313">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="173df-314">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-314">15 seconds</span></span> | <span data-ttu-id="173df-315">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="173df-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="173df-316">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="173df-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="173df-317">如果客户端没有在服务器上的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="173df-318">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="173df-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="173df-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="173df-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="173df-320">选项</span><span class="sxs-lookup"><span data-stu-id="173df-320">Option</span></span> | <span data-ttu-id="173df-321">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-321">Default value</span></span> | <span data-ttu-id="173df-322">描述</span><span class="sxs-lookup"><span data-stu-id="173df-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="173df-323">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-324">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-324">Timeout for server activity.</span></span> <span data-ttu-id="173df-325">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="173df-326">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-327">建议的值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="173df-328">15秒（15000 (毫秒)）</span><span class="sxs-lookup"><span data-stu-id="173df-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="173df-329">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="173df-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="173df-330">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="173df-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="173df-331">如果客户端没有在服务器上的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="173df-332">Java</span><span class="sxs-lookup"><span data-stu-id="173df-332">Java</span></span>](#tab/java)

| <span data-ttu-id="173df-333">选项</span><span class="sxs-lookup"><span data-stu-id="173df-333">Option</span></span> | <span data-ttu-id="173df-334">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-334">Default value</span></span> | <span data-ttu-id="173df-335">描述</span><span class="sxs-lookup"><span data-stu-id="173df-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="173df-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="173df-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="173df-337">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-338">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-338">Timeout for server activity.</span></span> <span data-ttu-id="173df-339">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="173df-340">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-341">建议的值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="173df-342">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-342">15 seconds</span></span> | <span data-ttu-id="173df-343">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="173df-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="173df-344">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="173df-345">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-346">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="173df-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="173df-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="173df-348">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="173df-349">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="173df-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="173df-350">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="173df-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="173df-351">如果客户端没有在服务器上的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="173df-352">.NET</span><span class="sxs-lookup"><span data-stu-id="173df-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="173df-353">选项</span><span class="sxs-lookup"><span data-stu-id="173df-353">Option</span></span> | <span data-ttu-id="173df-354">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-354">Default value</span></span> | <span data-ttu-id="173df-355">描述</span><span class="sxs-lookup"><span data-stu-id="173df-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="173df-356">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-357">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-357">Timeout for server activity.</span></span> <span data-ttu-id="173df-358">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为已断开连接，并触发 `Closed` 事件（JavaScript 中 `onclose`）。</span><span class="sxs-lookup"><span data-stu-id="173df-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="173df-359">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-360">建议的值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="173df-361">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-361">15 seconds</span></span> | <span data-ttu-id="173df-362">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="173df-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="173df-363">如果服务器未在此时间间隔内发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中 `onclose`）。</span><span class="sxs-lookup"><span data-stu-id="173df-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="173df-364">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-365">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="173df-366">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="173df-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="173df-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="173df-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="173df-368">选项</span><span class="sxs-lookup"><span data-stu-id="173df-368">Option</span></span> | <span data-ttu-id="173df-369">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-369">Default value</span></span> | <span data-ttu-id="173df-370">描述</span><span class="sxs-lookup"><span data-stu-id="173df-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="173df-371">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-372">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-372">Timeout for server activity.</span></span> <span data-ttu-id="173df-373">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="173df-374">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-375">建议的值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="173df-376">Java</span><span class="sxs-lookup"><span data-stu-id="173df-376">Java</span></span>](#tab/java)

| <span data-ttu-id="173df-377">选项</span><span class="sxs-lookup"><span data-stu-id="173df-377">Option</span></span> | <span data-ttu-id="173df-378">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-378">Default value</span></span> | <span data-ttu-id="173df-379">描述</span><span class="sxs-lookup"><span data-stu-id="173df-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="173df-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="173df-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="173df-381">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="173df-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="173df-382">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="173df-382">Timeout for server activity.</span></span> <span data-ttu-id="173df-383">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="173df-384">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="173df-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="173df-385">建议值至少为服务器的 @no__t 0 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="173df-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="173df-386">15 秒</span><span class="sxs-lookup"><span data-stu-id="173df-386">15 seconds</span></span> | <span data-ttu-id="173df-387">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="173df-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="173df-388">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 @no__t 0 事件。</span><span class="sxs-lookup"><span data-stu-id="173df-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="173df-389">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="173df-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="173df-390">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="173df-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="173df-391">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="173df-391">Configure additional options</span></span>

<span data-ttu-id="173df-392">可在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的 `withUrl`）或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="173df-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="173df-393">.NET</span><span class="sxs-lookup"><span data-stu-id="173df-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="173df-394">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="173df-394">.NET Option</span></span> |  <span data-ttu-id="173df-395">默认值</span><span class="sxs-lookup"><span data-stu-id="173df-395">Default value</span></span> | <span data-ttu-id="173df-396">描述</span><span class="sxs-lookup"><span data-stu-id="173df-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="173df-397">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="173df-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="173df-398">将此项设置为 `true` 将跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="173df-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="173df-399">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="173df-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="173df-400">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="173df-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="173df-401">空</span><span class="sxs-lookup"><span data-stu-id="173df-401">Empty</span></span> | <span data-ttu-id="173df-402">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="173df-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="173df-403">空</span><span class="sxs-lookup"><span data-stu-id="173df-403">Empty</span></span> | <span data-ttu-id="173df-404">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="173df-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="173df-405">空</span><span class="sxs-lookup"><span data-stu-id="173df-405">Empty</span></span> | <span data-ttu-id="173df-406">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="173df-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="173df-407">5 秒</span><span class="sxs-lookup"><span data-stu-id="173df-407">5 seconds</span></span> | <span data-ttu-id="173df-408">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="173df-408">WebSockets only.</span></span> <span data-ttu-id="173df-409">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="173df-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="173df-410">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="173df-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="173df-411">空</span><span class="sxs-lookup"><span data-stu-id="173df-411">Empty</span></span> | <span data-ttu-id="173df-412">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="173df-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="173df-413">一个委托，可用于配置或替换用于发送 HTTP 请求的 @no__t 0。</span><span class="sxs-lookup"><span data-stu-id="173df-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="173df-414">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="173df-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="173df-415">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="173df-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="173df-416">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="173df-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="173df-417">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="173df-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="173df-418">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="173df-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="173df-419">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="173df-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="173df-420">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="173df-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="173df-421">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="173df-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="173df-422">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="173df-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="173df-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="173df-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="173df-424">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="173df-424">JavaScript Option</span></span> | <span data-ttu-id="173df-425">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-425">Default Value</span></span> | <span data-ttu-id="173df-426">描述</span><span class="sxs-lookup"><span data-stu-id="173df-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="173df-427">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="173df-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="173df-428">将此项设置为 `true` 将跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="173df-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="173df-429">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="173df-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="173df-430">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="173df-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="173df-431">Java</span><span class="sxs-lookup"><span data-stu-id="173df-431">Java</span></span>](#tab/java)

| <span data-ttu-id="173df-432">Java 选项</span><span class="sxs-lookup"><span data-stu-id="173df-432">Java Option</span></span> | <span data-ttu-id="173df-433">Default Value</span><span class="sxs-lookup"><span data-stu-id="173df-433">Default Value</span></span> | <span data-ttu-id="173df-434">描述</span><span class="sxs-lookup"><span data-stu-id="173df-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="173df-435">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="173df-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="173df-436">将此项设置为 `true` 将跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="173df-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="173df-437">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="173df-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="173df-438">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="173df-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="173df-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="173df-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="173df-440">空</span><span class="sxs-lookup"><span data-stu-id="173df-440">Empty</span></span> | <span data-ttu-id="173df-441">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="173df-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="173df-442">在 .NET 客户端中，可以通过提供给 `WithUrl` 的 options 委托修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="173df-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="173df-443">在 JavaScript 客户端中，可以在提供给 `withUrl` 的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="173df-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="173df-444">在 Java 客户端中，可以通过从 @no__t 返回的 @no__t 上的方法配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="173df-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="173df-445">其他资源</span><span class="sxs-lookup"><span data-stu-id="173df-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
