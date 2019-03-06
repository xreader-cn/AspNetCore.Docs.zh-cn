---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何从服务器集线器方法返回的值的流和使用使用.NET 和 JavaScript 客户端的流。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: fb7183f7189d62c181f69ffdb170e3da25612919
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345582"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="a5941-103">使用 ASP.NET Core SignalR 中流式处理</span><span class="sxs-lookup"><span data-stu-id="a5941-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="a5941-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="a5941-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="a5941-105">ASP.NET Core SignalR 支持流式处理服务器方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="a5941-105">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="a5941-106">这是适用于其中的数据片段会随时间的方案。</span><span class="sxs-lookup"><span data-stu-id="a5941-106">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="a5941-107">当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="a5941-107">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="a5941-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a5941-108">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="a5941-109">设置中心</span><span class="sxs-lookup"><span data-stu-id="a5941-109">Set up the hub</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5941-110">集线器方法自动成为流式处理的集线器方法时它将返回`ChannelReader<T>`， `IAsyncEnumerable<T>`， `Task<ChannelReader<T>>`，或`Task<IAsyncEnumerable<T>>`。</span><span class="sxs-lookup"><span data-stu-id="a5941-110">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a5941-111">集线器方法自动成为流式处理的集线器方法时它将返回`ChannelReader<T>`或`Task<ChannelReader<T>>`。</span><span class="sxs-lookup"><span data-stu-id="a5941-111">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5941-112">在 ASP.NET Core 3.0 或更高版本，流式处理集线器方法可以返回`IAsyncEnumerable<T>`除了`ChannelReader<T>`。</span><span class="sxs-lookup"><span data-stu-id="a5941-112">In ASP.NET Core 3.0 or later, streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="a5941-113">若要返回的最简单方法`IAsyncEnumerable<T>`是使集线器方法的异步迭代器方法，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="a5941-113">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="a5941-114">中心异步迭代器方法可接受`CancellationToken`从流的客户端取消订阅时，将触发的参数。</span><span class="sxs-lookup"><span data-stu-id="a5941-114">Hub async iterator methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="a5941-115">异步迭代器方法轻松地避免出现频道常见的问题，如不返回`ChannelReader`足够早或未完成的情况下退出方法`ChannelWriter`。</span><span class="sxs-lookup"><span data-stu-id="a5941-115">Async iterator methods easily avoid problems common with Channels such as not returning the `ChannelReader` early enough or exiting the method without completing the `ChannelWriter`.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="a5941-116">下面的示例显示了数据流式传输到客户端使用通道的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a5941-116">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="a5941-117">每当将对象写入到`ChannelWriter`该对象将立即发送给客户端。</span><span class="sxs-lookup"><span data-stu-id="a5941-117">Whenever an object is written to the `ChannelWriter` that object is immediately sent to the client.</span></span> <span data-ttu-id="a5941-118">在结束时，`ChannelWriter`完成告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="a5941-118">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="a5941-119">写入`ChannelWriter`在后台线程并返回`ChannelReader`越早越好。</span><span class="sxs-lookup"><span data-stu-id="a5941-119">Write to the `ChannelWriter` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="a5941-120">将阻止其他集线器调用，直到`ChannelReader`返回。</span><span class="sxs-lookup"><span data-stu-id="a5941-120">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="a5941-121">包装中的逻辑`try ... catch`并完成`Channel`catch 和外部的关键点，以确保在中心正确完成方法调用。</span><span class="sxs-lookup"><span data-stu-id="a5941-121">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="a5941-122">在 ASP.NET Core 2.2 或更高版本，流式处理集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，将触发的参数。</span><span class="sxs-lookup"><span data-stu-id="a5941-122">In ASP.NET Core 2.2 or later, streaming hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="a5941-123">使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。</span><span class="sxs-lookup"><span data-stu-id="a5941-123">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="a5941-124">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="a5941-124">.NET client</span></span>

<span data-ttu-id="a5941-125">`StreamAsChannelAsync`方法`HubConnection`用于调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="a5941-125">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="a5941-126">将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="a5941-126">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="a5941-127">上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="a5941-127">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="a5941-128">一个`ChannelReader<T>`返回从流调用，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="a5941-128">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="a5941-129">若要读取数据，常见模式是循环访问`WaitToReadAsync`，并调用`TryRead`数据时可用。</span><span class="sxs-lookup"><span data-stu-id="a5941-129">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="a5941-130">循环将结束时该流已关闭服务器，或取消标记传递给`StreamAsChannelAsync`被取消。</span><span class="sxs-lookup"><span data-stu-id="a5941-130">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

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

## <a name="javascript-client"></a><span data-ttu-id="a5941-131">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="a5941-131">JavaScript client</span></span>

<span data-ttu-id="a5941-132">JavaScript 客户端调用集线器上流式处理方法使用`connection.stream`。</span><span class="sxs-lookup"><span data-stu-id="a5941-132">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="a5941-133">`stream`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="a5941-133">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="a5941-134">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="a5941-134">The name of the hub method.</span></span> <span data-ttu-id="a5941-135">在以下示例中，中心方法名称是`Counter`。</span><span class="sxs-lookup"><span data-stu-id="a5941-135">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="a5941-136">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="a5941-136">Arguments defined in the hub method.</span></span> <span data-ttu-id="a5941-137">在以下示例中，参数是： 若要接收的流项和流项之间的延迟的数量的计数。</span><span class="sxs-lookup"><span data-stu-id="a5941-137">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="a5941-138">`connection.stream` 返回`IStreamResult`其中包含`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="a5941-138">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="a5941-139">传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以获取来自通知`stream`调用。</span><span class="sxs-lookup"><span data-stu-id="a5941-139">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a5941-140">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="a5941-140">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a5941-141">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="a5941-141">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="a5941-142">调用此方法将导致`CancellationToken`（如果提供） 的集线器方法的参数以取消。</span><span class="sxs-lookup"><span data-stu-id="a5941-142">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
## <a name="java-client"></a><span data-ttu-id="a5941-143">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="a5941-143">Java client</span></span>
<span data-ttu-id="a5941-144">SignalR Java 客户端使用`stream`方法调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="a5941-144">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="a5941-145">它接受三个或多个参数：</span><span class="sxs-lookup"><span data-stu-id="a5941-145">It accepts three or more arguments:</span></span>

* <span data-ttu-id="a5941-146">流项的预期的类型</span><span class="sxs-lookup"><span data-stu-id="a5941-146">The expected type of the stream items</span></span> 
* <span data-ttu-id="a5941-147">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="a5941-147">The name of the hub method.</span></span>
* <span data-ttu-id="a5941-148">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="a5941-148">Arguments defined in the hub method.</span></span> 

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```
<span data-ttu-id="a5941-149">`stream`方法`HubConnection`返回流项类型的可观察量。</span><span class="sxs-lookup"><span data-stu-id="a5941-149">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="a5941-150">可观察的类型`subscribe`方法是在其中定义你`onNext`，`onError`和`onCompleted`处理程序。</span><span class="sxs-lookup"><span data-stu-id="a5941-150">The Observable type's `subscribe` method is where you define your `onNext`,  `onError` and  `onCompleted` handlers.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="a5941-151">相关资源</span><span class="sxs-lookup"><span data-stu-id="a5941-151">Related resources</span></span>

* [<span data-ttu-id="a5941-152">中心</span><span class="sxs-lookup"><span data-stu-id="a5941-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a5941-153">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="a5941-153">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a5941-154">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="a5941-154">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="a5941-155">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="a5941-155">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
