---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何在客户端和服务器之间流式传输数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: d520c8eec3e777acb9604bdcb5969268deabf8da
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186926"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="4fc2d-103">使用 ASP.NET Core SignalR 中流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="4fc2d-104">作者： [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="4fc2d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4fc2d-105">ASP.NET Core SignalR 支持从客户端到服务器以及从服务器到客户端的流式传输。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="4fc2d-106">这适用于数据片段随着时间的推移而发生的情况。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="4fc2d-107">流式传输时，每个片段一旦变为可用，就会发送到客户端或服务器，而不是等待所有数据都可用。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4fc2d-108">ASP.NET Core SignalR 支持流式处理服务器方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="4fc2d-109">这适用于数据片段随着时间的推移而发生的情况。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="4fc2d-110">将返回值流式传输到客户端时，每个片段会在其可用时立即发送到客户端，而不是等待所有数据都可用。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="4fc2d-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4fc2d-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="4fc2d-112">设置用于流式传输的集线器</span><span class="sxs-lookup"><span data-stu-id="4fc2d-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4fc2d-113"><xref:System.Collections.Generic.IAsyncEnumerable`1>当集线器方法返回<xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>`、、或时，它会自动成为流式处理中心方法。`Task<IAsyncEnumerable<T>>`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4fc2d-114">当集线器方法返回<xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>`或时，它会自动成为流式处理中心方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="4fc2d-115">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4fc2d-116">除了之外， `ChannelReader<T>`流集线器`IAsyncEnumerable<T>`方法还可以返回。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="4fc2d-117">返回`IAsyncEnumerable<T>`的最简单方法是将集线器方法设为异步迭代器方法，如下例所示。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="4fc2d-118">中心异步迭代器方法可以接受`CancellationToken`当客户端从流中取消订阅时触发的参数。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="4fc2d-119">异步迭代器方法避免了与通道常见的问题，例如， `ChannelReader`在没有完成的<xref:System.Threading.Channels.ChannelWriter`1>情况下，不能提前返回或退出方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="4fc2d-120">下面的示例演示了使用通道将数据流式传输到客户端的基础知识。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="4fc2d-121">每当将对象写入到<xref:System.Threading.Channels.ChannelWriter%601>时，都会立即将对象发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="4fc2d-122">结束时，已完成`ChannelWriter` ，告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="4fc2d-123">在后台线程上写入，并尽快`ChannelReader`返回。 `ChannelWriter<T>`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="4fc2d-124">在返回之前`ChannelReader` ，其他中心调用会被阻止。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="4fc2d-125">在中`try ... catch`环绕逻辑。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="4fc2d-126">`Channel`完成中`catch`和之外`catch`的，以确保中心方法调用正确完成。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4fc2d-127">服务器到客户端流式处理中心方法可以接受`CancellationToken`当客户端从流中取消订阅时触发的参数。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="4fc2d-128">如果客户端在流末尾之前断开连接，请使用此标记停止服务器操作并释放任何资源。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="4fc2d-129">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-129">Client-to-server streaming</span></span>

<span data-ttu-id="4fc2d-130">当某个集线器方法接受一个或多个类型<xref:System.Threading.Channels.ChannelReader%601>为或<xref:System.Collections.Generic.IAsyncEnumerable%601>的对象时，它会自动成为客户端到服务器的流式处理中心方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="4fc2d-131">下面的示例演示了读取从客户端发送的流式处理数据的基础知识。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="4fc2d-132">每当客户端向<xref:System.Threading.Channels.ChannelWriter%601>中写入数据时，数据就会写入中心方法所读取的服务器上的。 `ChannelReader`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="4fc2d-133">下面<xref:System.Collections.Generic.IAsyncEnumerable%601>是方法的版本。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="4fc2d-134">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="4fc2d-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="4fc2d-135">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4fc2d-136">上`StreamAsync` `StreamAsChannelAsync`的和方法用于调用服务器到客户端的流式处理方法。 `HubConnection`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="4fc2d-137">将 hub 方法中定义的集线器方法名称和参数传递给`StreamAsync`或`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="4fc2d-138">`StreamAsync<T>` 和`StreamAsChannelAsync<T>`上的泛型参数指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="4fc2d-139">类型`IAsyncEnumerable<T>`为或`ChannelReader<T>`的对象将从流调用返回，并表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="4fc2d-140">`StreamAsync` 返回`IAsyncEnumerable<int>`以下内容的示例：</span><span class="sxs-lookup"><span data-stu-id="4fc2d-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="4fc2d-141">`StreamAsChannelAsync` 返回`ChannelReader<int>`的相应示例：</span><span class="sxs-lookup"><span data-stu-id="4fc2d-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4fc2d-142">`StreamAsChannelAsync` 上`HubConnection`的方法用于调用服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="4fc2d-143">将中心方法中定义的集线器方法名称和参数传递给`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="4fc2d-144">上`StreamAsChannelAsync<T>`的泛型参数指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="4fc2d-145">`ChannelReader<T>`从流调用返回，表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="4fc2d-146">`StreamAsChannelAsync` 上`HubConnection`的方法用于调用服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="4fc2d-147">将中心方法中定义的集线器方法名称和参数传递给`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="4fc2d-148">上`StreamAsChannelAsync<T>`的泛型参数指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="4fc2d-149">`ChannelReader<T>`从流调用返回，表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="4fc2d-150">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-150">Client-to-server streaming</span></span>

<span data-ttu-id="4fc2d-151">可以通过两种方法从 .NET 客户端调用客户端到服务器的流式处理中心方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="4fc2d-152">`IAsyncEnumerable<T>`可以将`SendAsync` `InvokeAsync`或作为参数传入、或`StreamAsChannelAsync`，具体取决于所调用的中心方法。 `ChannelReader`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="4fc2d-153">只要将数据写入到`IAsyncEnumerable`或`ChannelWriter`对象，服务器上的集线器方法就会收到来自客户端的数据的新项。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="4fc2d-154">如果使用`IAsyncEnumerable`对象，则流在返回流项的方法退出后结束。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="4fc2d-155">或者，如果使用的是`ChannelWriter`，请使用`channel.Writer.Complete()`以下内容完成通道：</span><span class="sxs-lookup"><span data-stu-id="4fc2d-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="4fc2d-156">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="4fc2d-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="4fc2d-157">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-157">Server-to-client streaming</span></span>

<span data-ttu-id="4fc2d-158">JavaScript 客户端通过调用与`connection.stream`的集线器上的服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="4fc2d-159">`stream`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="4fc2d-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="4fc2d-160">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-160">The name of the hub method.</span></span> <span data-ttu-id="4fc2d-161">在下面的示例中，中心方法名称是`Counter`。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="4fc2d-162">在 hub 方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="4fc2d-163">在下面的示例中，参数是要接收的流项数的计数以及流项之间的延迟。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="4fc2d-164">`connection.stream`返回一个`IStreamResult`，它`subscribe`包含方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="4fc2d-165">`next` `stream`向传递，并设置、`error`和`complete`回调，以接收来自调用的通知。 `subscribe` `IStreamSubscriber`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="4fc2d-166">若要从客户端结束流，请`dispose` `ISubscription`对从`subscribe`方法返回的调用方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="4fc2d-167">如果提供了，则调用此`CancellationToken`方法会导致取消集线器方法的参数。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="4fc2d-168">若要从客户端结束流，请`dispose` `ISubscription`对从`subscribe`方法返回的调用方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="4fc2d-169">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-169">Client-to-server streaming</span></span>

<span data-ttu-id="4fc2d-170">JavaScript 客户端通过`Subject`将作为`send`自变量传入、 `invoke`或`stream`，来调用集线器上的客户端到服务器流式处理方法，具体取决于所调用的集线器方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="4fc2d-171">是一个类似`Subject`于的类。 `Subject`</span><span class="sxs-lookup"><span data-stu-id="4fc2d-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="4fc2d-172">例如，在 RxJS 中，可以使用该库中的[Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)类。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="4fc2d-173">使用`subject.next(item)`项调用会将项写入流，集线器方法接收服务器上的项。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="4fc2d-174">若要结束流，请`subject.complete()`调用。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="4fc2d-175">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="4fc2d-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="4fc2d-176">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="4fc2d-176">Server-to-client streaming</span></span>

<span data-ttu-id="4fc2d-177">SignalR Java 客户端使用`stream`方法调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="4fc2d-178">`stream`接受三个或更多参数：</span><span class="sxs-lookup"><span data-stu-id="4fc2d-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="4fc2d-179">流项的预期类型。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="4fc2d-180">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-180">The name of the hub method.</span></span>
* <span data-ttu-id="4fc2d-181">在 hub 方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="4fc2d-182">`stream` 上`HubConnection`的方法返回流项类型的可观察对象。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="4fc2d-183">可观察的类型`subscribe`的方法是`onNext`定义`onError`的`onCompleted`位置。</span><span class="sxs-lookup"><span data-stu-id="4fc2d-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4fc2d-184">其他资源</span><span class="sxs-lookup"><span data-stu-id="4fc2d-184">Additional resources</span></span>

* [<span data-ttu-id="4fc2d-185">中心</span><span class="sxs-lookup"><span data-stu-id="4fc2d-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4fc2d-186">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="4fc2d-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4fc2d-187">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="4fc2d-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="4fc2d-188">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="4fc2d-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
