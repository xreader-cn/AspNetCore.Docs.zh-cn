---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何进行流式处理客户端和服务器之间的数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: 83bbb231482d9c1606be3c5bbbeb1cc3b8efcf7d
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982651"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="0b76a-103">使用 ASP.NET Core SignalR 中流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="0b76a-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="0b76a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b76a-105">ASP.NET Core SignalR 支持流式处理从客户端到服务器和服务器到客户端。</span><span class="sxs-lookup"><span data-stu-id="0b76a-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="0b76a-106">这是适用于方案的数据片段随着时间的推移到达的位置。</span><span class="sxs-lookup"><span data-stu-id="0b76a-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="0b76a-107">当流式处理，每个片段是发送到客户端或服务器变得可用，而不是等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="0b76a-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b76a-108">ASP.NET Core SignalR 支持流式处理服务器方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="0b76a-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="0b76a-109">这是适用于方案的数据片段随着时间的推移到达的位置。</span><span class="sxs-lookup"><span data-stu-id="0b76a-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="0b76a-110">当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="0b76a-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="0b76a-111">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0b76a-111">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="0b76a-112">设置为流式处理中心</span><span class="sxs-lookup"><span data-stu-id="0b76a-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b76a-113">集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Threading.Channels.ChannelReader`1>， `IAsyncEnumerable<T>`， `Task<ChannelReader<T>>`，或`Task<IAsyncEnumerable<T>>`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-113">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader`1>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b76a-114">集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Threading.Channels.ChannelReader`1>或`Task<ChannelReader<T>>`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader`1> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="0b76a-115">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b76a-116">流式处理集线器方法可以返回`IAsyncEnumerable<T>`除了`ChannelReader<T>`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="0b76a-117">若要返回的最简单方法`IAsyncEnumerable<T>`是使集线器方法的异步迭代器方法，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="0b76a-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="0b76a-118">中心异步迭代器方法可接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="0b76a-119">异步迭代器方法避免通道，常见的问题，如不返回`ChannelReader`足够早或未完成的情况下退出方法<xref:System.Threading.Channels.ChannelWriter`1>。</span><span class="sxs-lookup"><span data-stu-id="0b76a-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="0b76a-120">下面的示例显示了数据流式传输到客户端使用通道的基础知识。</span><span class="sxs-lookup"><span data-stu-id="0b76a-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="0b76a-121">每当将对象写入到<xref:System.Threading.Channels.ChannelWriter`1>，该对象将立即发送给客户端。</span><span class="sxs-lookup"><span data-stu-id="0b76a-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter`1>, the object is immediately sent to the client.</span></span> <span data-ttu-id="0b76a-122">在结束时，`ChannelWriter`完成告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="0b76a-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="0b76a-123">写入`ChannelWriter<T>`在后台线程并返回`ChannelReader`越早越好。</span><span class="sxs-lookup"><span data-stu-id="0b76a-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="0b76a-124">其他集线器调用被阻止，直到`ChannelReader`返回。</span><span class="sxs-lookup"><span data-stu-id="0b76a-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="0b76a-125">包装中的逻辑`try ... catch`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="0b76a-126">完成`Channel`中`catch`和外部`catch`以确保集线器方法调用是否正确完成。</span><span class="sxs-lookup"><span data-stu-id="0b76a-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="0b76a-127">服务器到客户端流式处理的集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="0b76a-128">使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。</span><span class="sxs-lookup"><span data-stu-id="0b76a-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="0b76a-129">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-129">Client-to-server streaming</span></span>

<span data-ttu-id="0b76a-130">当它接受一个或多个中心方法将自动成为客户端-服务器流式处理集线器方法<xref:System.Threading.Channels.ChannelReader`1>s。</span><span class="sxs-lookup"><span data-stu-id="0b76a-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more <xref:System.Threading.Channels.ChannelReader`1>s.</span></span> <span data-ttu-id="0b76a-131">下面的示例演示读取从客户端发送的流式处理数据的基础知识。</span><span class="sxs-lookup"><span data-stu-id="0b76a-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="0b76a-132">每当客户端写入<xref:System.Threading.Channels.ChannelWriter`1>，将数据写入到`ChannelReader`集线器方法读取从服务器上。</span><span class="sxs-lookup"><span data-stu-id="0b76a-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter`1>, the data is written into the `ChannelReader` on the server that the hub method is reading from.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="0b76a-133">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="0b76a-133">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="0b76a-134">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-134">Server-to-client streaming</span></span>

<span data-ttu-id="0b76a-135">`StreamAsChannelAsync`方法`HubConnection`用于调用服务器到客户端的流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="0b76a-135">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="0b76a-136">将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-136">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="0b76a-137">上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="0b76a-137">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="0b76a-138">一个`ChannelReader<T>`流调用中返回，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="0b76a-138">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="0b76a-139">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-139">Client-to-server streaming</span></span>

<span data-ttu-id="0b76a-140">若要调用客户端-服务器流式处理集线器方法从.NET 客户端，创建`Channel`并传递`ChannelReader`的参数作为`SendAsync`， `InvokeAsync`，或`StreamAsChannelAsync`，取决于集线器方法调用。</span><span class="sxs-lookup"><span data-stu-id="0b76a-140">To invoke a client-to-server streaming hub method from the .NET client, create a `Channel` and pass the `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="0b76a-141">每当将数据写入到`ChannelWriter`，在服务器上的集线器方法从客户端接收的数据的新项。</span><span class="sxs-lookup"><span data-stu-id="0b76a-141">Whenever data is written to the `ChannelWriter`, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="0b76a-142">若要结束该流，请完成与通道`channel.Writer.Complete()`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-142">To end the stream, complete the channel with `channel.Writer.Complete()`.</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="0b76a-143">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="0b76a-143">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="0b76a-144">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-144">Server-to-client streaming</span></span>

<span data-ttu-id="0b76a-145">JavaScript 客户端上使用的集线器调用服务器到客户端流式处理方法`connection.stream`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-145">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="0b76a-146">`stream`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="0b76a-146">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="0b76a-147">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="0b76a-147">The name of the hub method.</span></span> <span data-ttu-id="0b76a-148">在以下示例中，中心方法名称是`Counter`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-148">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="0b76a-149">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-149">Arguments defined in the hub method.</span></span> <span data-ttu-id="0b76a-150">在下面的示例中的参数是要接收的流项和流项之间的延迟的数量的计数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-150">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="0b76a-151">`connection.stream` 返回`IStreamResult`，其中包含`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="0b76a-151">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="0b76a-152">传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以接收来自通知`stream`调用。</span><span class="sxs-lookup"><span data-stu-id="0b76a-152">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="0b76a-153">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="0b76a-153">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="0b76a-154">调用此方法可导致取消`CancellationToken`集线器方法，如果您提供了一个参数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-154">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="0b76a-155">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="0b76a-155">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="0b76a-156">客户端到服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-156">Client-to-server streaming</span></span>

<span data-ttu-id="0b76a-157">通过传入对中心 JavaScript 客户端调用客户端-服务器流式处理方法`Subject`作为参数`send`， `invoke`，或`stream`，取决于集线器方法调用。</span><span class="sxs-lookup"><span data-stu-id="0b76a-157">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="0b76a-158">`Subject`是一个类，如下所示`Subject`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-158">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="0b76a-159">例如在 RxJS 中，您可以使用[使用者](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)该库中的类。</span><span class="sxs-lookup"><span data-stu-id="0b76a-159">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="0b76a-160">调用`subject.next(item)`与某项的项写入流，和集线器方法接收服务器上的项。</span><span class="sxs-lookup"><span data-stu-id="0b76a-160">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="0b76a-161">若要结束该流，请调用`subject.complete()`。</span><span class="sxs-lookup"><span data-stu-id="0b76a-161">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="0b76a-162">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="0b76a-162">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="0b76a-163">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="0b76a-163">Server-to-client streaming</span></span>

<span data-ttu-id="0b76a-164">SignalR Java 客户端使用`stream`方法调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="0b76a-164">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="0b76a-165">`stream` 接受三个或多个参数：</span><span class="sxs-lookup"><span data-stu-id="0b76a-165">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="0b76a-166">流项的预期的类型。</span><span class="sxs-lookup"><span data-stu-id="0b76a-166">The expected type of the stream items.</span></span>
* <span data-ttu-id="0b76a-167">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="0b76a-167">The name of the hub method.</span></span>
* <span data-ttu-id="0b76a-168">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="0b76a-168">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="0b76a-169">`stream`方法`HubConnection`返回流项类型的可观察量。</span><span class="sxs-lookup"><span data-stu-id="0b76a-169">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="0b76a-170">可观察的类型`subscribe`方法是，在哪里`onNext`，`onError`和`onCompleted`定义处理程序。</span><span class="sxs-lookup"><span data-stu-id="0b76a-170">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0b76a-171">其他资源</span><span class="sxs-lookup"><span data-stu-id="0b76a-171">Additional resources</span></span>

* [<span data-ttu-id="0b76a-172">中心</span><span class="sxs-lookup"><span data-stu-id="0b76a-172">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0b76a-173">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="0b76a-173">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b76a-174">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="0b76a-174">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="0b76a-175">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="0b76a-175">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
