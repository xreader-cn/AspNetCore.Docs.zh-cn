---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何进行流式处理客户端和服务器之间的数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: a75156f398e113393ddb891d16eec3f09de80c09
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750184"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="071c8-103">使用 ASP.NET Core SignalR 中流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="071c8-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="071c8-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="071c8-105">ASP.NET Core SignalR 支持流式处理从客户端到服务器和服务器到客户端。</span><span class="sxs-lookup"><span data-stu-id="071c8-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="071c8-106">这是适用于方案的数据片段随着时间的推移到达的位置。</span><span class="sxs-lookup"><span data-stu-id="071c8-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="071c8-107">当流式处理，每个片段是发送到客户端或服务器变得可用，而不是等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="071c8-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="071c8-108">ASP.NET Core SignalR 支持流式处理服务器方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="071c8-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="071c8-109">这是适用于方案的数据片段随着时间的推移到达的位置。</span><span class="sxs-lookup"><span data-stu-id="071c8-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="071c8-110">当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="071c8-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="071c8-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="071c8-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="071c8-112">设置为流式处理中心</span><span class="sxs-lookup"><span data-stu-id="071c8-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="071c8-113">集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Collections.Generic.IAsyncEnumerable`1>， <xref:System.Threading.Channels.ChannelReader%601>， `Task<IAsyncEnumerable<T>>`，或`Task<ChannelReader<T>>`。</span><span class="sxs-lookup"><span data-stu-id="071c8-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="071c8-114">集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Threading.Channels.ChannelReader%601>或`Task<ChannelReader<T>>`。</span><span class="sxs-lookup"><span data-stu-id="071c8-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="071c8-115">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="071c8-116">流式处理集线器方法可以返回`IAsyncEnumerable<T>`除了`ChannelReader<T>`。</span><span class="sxs-lookup"><span data-stu-id="071c8-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="071c8-117">若要返回的最简单方法`IAsyncEnumerable<T>`是使集线器方法的异步迭代器方法，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="071c8-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="071c8-118">中心异步迭代器方法可接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。</span><span class="sxs-lookup"><span data-stu-id="071c8-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="071c8-119">异步迭代器方法避免通道，常见的问题，如不返回`ChannelReader`足够早或未完成的情况下退出方法<xref:System.Threading.Channels.ChannelWriter`1>。</span><span class="sxs-lookup"><span data-stu-id="071c8-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="071c8-120">下面的示例显示了数据流式传输到客户端使用通道的基础知识。</span><span class="sxs-lookup"><span data-stu-id="071c8-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="071c8-121">每当将对象写入到<xref:System.Threading.Channels.ChannelWriter%601>，该对象将立即发送给客户端。</span><span class="sxs-lookup"><span data-stu-id="071c8-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="071c8-122">在结束时，`ChannelWriter`完成告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="071c8-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="071c8-123">写入`ChannelWriter<T>`在后台线程并返回`ChannelReader`越早越好。</span><span class="sxs-lookup"><span data-stu-id="071c8-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="071c8-124">其他集线器调用被阻止，直到`ChannelReader`返回。</span><span class="sxs-lookup"><span data-stu-id="071c8-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="071c8-125">包装中的逻辑`try ... catch`。</span><span class="sxs-lookup"><span data-stu-id="071c8-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="071c8-126">完成`Channel`中`catch`和外部`catch`以确保集线器方法调用是否正确完成。</span><span class="sxs-lookup"><span data-stu-id="071c8-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="071c8-127">服务器到客户端流式处理的集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。</span><span class="sxs-lookup"><span data-stu-id="071c8-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="071c8-128">使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。</span><span class="sxs-lookup"><span data-stu-id="071c8-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="071c8-129">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-129">Client-to-server streaming</span></span>

<span data-ttu-id="071c8-130">它接受一个或多个类型的对象时集线器方法将自动成为客户端-服务器流式处理集线器方法<xref:System.Threading.Channels.ChannelReader%601>或<xref:System.Collections.Generic.IAsyncEnumerable%601>。</span><span class="sxs-lookup"><span data-stu-id="071c8-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="071c8-131">下面的示例演示读取从客户端发送的流式处理数据的基础知识。</span><span class="sxs-lookup"><span data-stu-id="071c8-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="071c8-132">每当客户端写入<xref:System.Threading.Channels.ChannelWriter%601>，将数据写入到`ChannelReader`从中读取的集线器方法在服务器上。</span><span class="sxs-lookup"><span data-stu-id="071c8-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="071c8-133"><xref:System.Collections.Generic.IAsyncEnumerable%601>遵循版本的方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<Stream> stream) 
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="071c8-134">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="071c8-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="071c8-135">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="071c8-136">`StreamAsync`并`StreamAsChannelAsync`上的方法`HubConnection`用于调用服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="071c8-137">将中心方法名称和到中心方法中定义的自变量传递`StreamAsync`或`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="071c8-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="071c8-138">上的泛型参数`StreamAsync<T>`和`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="071c8-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="071c8-139">类型的对象`IAsyncEnumerable<T>`或`ChannelReader<T>`流调用中返回，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="071c8-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="071c8-140">一个`StreamAsync`返回的示例`IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="071c8-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="071c8-141">相应`StreamAsChannelAsync`返回的示例`ChannelReader<int>`:</span><span class="sxs-lookup"><span data-stu-id="071c8-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="071c8-142">`StreamAsChannelAsync`方法`HubConnection`用于调用服务器到客户端的流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="071c8-143">将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="071c8-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="071c8-144">上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="071c8-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="071c8-145">一个`ChannelReader<T>`流调用中返回，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="071c8-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="071c8-146">`StreamAsChannelAsync`方法`HubConnection`用于调用服务器到客户端的流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="071c8-147">将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="071c8-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="071c8-148">上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="071c8-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="071c8-149">一个`ChannelReader<T>`流调用中返回，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="071c8-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="071c8-150">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-150">Client-to-server streaming</span></span>

<span data-ttu-id="071c8-151">有两种方法来调用客户端-服务器流式处理集线器方法从.NET 客户端。</span><span class="sxs-lookup"><span data-stu-id="071c8-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="071c8-152">你可以在 pass`IAsyncEnumerable<T>`或`ChannelReader`的参数作为`SendAsync`， `InvokeAsync`，或`StreamAsChannelAsync`，取决于集线器方法调用。</span><span class="sxs-lookup"><span data-stu-id="071c8-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="071c8-153">每当将数据写入到`IAsyncEnumerable`或`ChannelWriter`对象，在服务器上的集线器方法从客户端接收的数据的新项。</span><span class="sxs-lookup"><span data-stu-id="071c8-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="071c8-154">如果使用`IAsyncEnumerable`对象，在流结束后返回流项退出该方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="071c8-155">或如果您使用的`ChannelWriter`，完成与通道`channel.Writer.Complete()`:</span><span class="sxs-lookup"><span data-stu-id="071c8-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="071c8-156">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="071c8-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="071c8-157">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-157">Server-to-client streaming</span></span>

<span data-ttu-id="071c8-158">JavaScript 客户端上使用的集线器调用服务器到客户端流式处理方法`connection.stream`。</span><span class="sxs-lookup"><span data-stu-id="071c8-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="071c8-159">`stream`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="071c8-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="071c8-160">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="071c8-160">The name of the hub method.</span></span> <span data-ttu-id="071c8-161">在以下示例中，中心方法名称是`Counter`。</span><span class="sxs-lookup"><span data-stu-id="071c8-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="071c8-162">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="071c8-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="071c8-163">在下面的示例中的参数是要接收的流项和流项之间的延迟的数量的计数。</span><span class="sxs-lookup"><span data-stu-id="071c8-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="071c8-164">`connection.stream` 返回`IStreamResult`，其中包含`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="071c8-165">传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以接收来自通知`stream`调用。</span><span class="sxs-lookup"><span data-stu-id="071c8-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="071c8-166">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="071c8-167">调用此方法可导致取消`CancellationToken`集线器方法，如果您提供了一个参数。</span><span class="sxs-lookup"><span data-stu-id="071c8-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="071c8-168">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="071c8-169">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-169">Client-to-server streaming</span></span>

<span data-ttu-id="071c8-170">通过传入对中心 JavaScript 客户端调用客户端-服务器流式处理方法`Subject`作为参数`send`， `invoke`，或`stream`，取决于集线器方法调用。</span><span class="sxs-lookup"><span data-stu-id="071c8-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="071c8-171">`Subject`是一个类，如下所示`Subject`。</span><span class="sxs-lookup"><span data-stu-id="071c8-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="071c8-172">例如在 RxJS 中，您可以使用[使用者](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)该库中的类。</span><span class="sxs-lookup"><span data-stu-id="071c8-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="071c8-173">调用`subject.next(item)`与某项的项写入流，和集线器方法接收服务器上的项。</span><span class="sxs-lookup"><span data-stu-id="071c8-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="071c8-174">若要结束该流，请调用`subject.complete()`。</span><span class="sxs-lookup"><span data-stu-id="071c8-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="071c8-175">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="071c8-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="071c8-176">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="071c8-176">Server-to-client streaming</span></span>

<span data-ttu-id="071c8-177">SignalR Java 客户端使用`stream`方法调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="071c8-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="071c8-178">`stream` 接受三个或多个参数：</span><span class="sxs-lookup"><span data-stu-id="071c8-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="071c8-179">流项的预期的类型。</span><span class="sxs-lookup"><span data-stu-id="071c8-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="071c8-180">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="071c8-180">The name of the hub method.</span></span>
* <span data-ttu-id="071c8-181">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="071c8-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="071c8-182">`stream`方法`HubConnection`返回流项类型的可观察量。</span><span class="sxs-lookup"><span data-stu-id="071c8-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="071c8-183">可观察的类型`subscribe`方法是，在哪里`onNext`，`onError`和`onCompleted`定义处理程序。</span><span class="sxs-lookup"><span data-stu-id="071c8-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="071c8-184">其他资源</span><span class="sxs-lookup"><span data-stu-id="071c8-184">Additional resources</span></span>

* [<span data-ttu-id="071c8-185">中心</span><span class="sxs-lookup"><span data-stu-id="071c8-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="071c8-186">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="071c8-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="071c8-187">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="071c8-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="071c8-188">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="071c8-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
