---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: ''
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: ade2d6fb6e799d53ff3aaa69c641d0088acdee95
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837398"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="3d6f1-102">使用 ASP.NET Core SignalR 中流式处理</span><span class="sxs-lookup"><span data-stu-id="3d6f1-102">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="3d6f1-103">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="3d6f1-103">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="3d6f1-104">ASP.NET Core SignalR 支持流式处理服务器方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-104">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="3d6f1-105">这是适用于其中的数据片段会随时间的方案。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-105">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="3d6f1-106">当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-106">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="3d6f1-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3d6f1-107">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="3d6f1-108">设置中心</span><span class="sxs-lookup"><span data-stu-id="3d6f1-108">Set up the hub</span></span>

<span data-ttu-id="3d6f1-109">集线器方法自动成为流式处理的集线器方法时它将返回`ChannelReader<T>`或`Task<ChannelReader<T>>`。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-109">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span> <span data-ttu-id="3d6f1-110">下面是一个示例，显示的数据流式传输到客户端的基础知识。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-110">Below is a sample that shows the basics of streaming data to the client.</span></span> <span data-ttu-id="3d6f1-111">每当将对象写入到`ChannelReader`该对象将立即发送给客户端。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-111">Whenever an object is written to the `ChannelReader` that object is immediately sent to the client.</span></span> <span data-ttu-id="3d6f1-112">在结束时，`ChannelReader`完成告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-112">At the end, the `ChannelReader` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="3d6f1-113">写入`ChannelReader`在后台线程并返回`ChannelReader`越早越好。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-113">Write to the `ChannelReader` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="3d6f1-114">将阻止其他集线器调用，直到`ChannelReader`返回。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-114">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="3d6f1-115">包装中的逻辑`try ... catch`并完成`Channel`catch 和外部的关键点，以确保在中心正确完成方法调用。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-115">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="3d6f1-116">在 ASP.NET Core 2.2 或更高版本，流式处理集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，将触发的参数。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-116">In ASP.NET Core 2.2 or later, streaming Hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="3d6f1-117">使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-117">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="3d6f1-118">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="3d6f1-118">.NET client</span></span>

<span data-ttu-id="3d6f1-119">`StreamAsChannelAsync`方法`HubConnection`用于调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-119">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="3d6f1-120">将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-120">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="3d6f1-121">上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-121">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="3d6f1-122">一个`ChannelReader<T>`返回从流调用，并且表示在客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-122">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="3d6f1-123">若要读取数据，常见模式是循环访问`WaitToReadAsync`，并调用`TryRead`数据时可用。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-123">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="3d6f1-124">循环将结束时该流已关闭服务器，或取消标记传递给`StreamAsChannelAsync`被取消。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-124">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to 
// the server, which will trigger the corresponding token in the Hub method.
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

## <a name="javascript-client"></a><span data-ttu-id="3d6f1-125">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="3d6f1-125">JavaScript client</span></span>

<span data-ttu-id="3d6f1-126">JavaScript 客户端调用集线器上流式处理方法使用`connection.stream`。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-126">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="3d6f1-127">`stream`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="3d6f1-127">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="3d6f1-128">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-128">The name of the hub method.</span></span> <span data-ttu-id="3d6f1-129">在以下示例中，中心方法名称是`Counter`。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-129">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="3d6f1-130">在集线器方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-130">Arguments defined in the hub method.</span></span> <span data-ttu-id="3d6f1-131">在以下示例中，参数是： 若要接收的流项和流项之间的延迟的数量的计数。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-131">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="3d6f1-132">`connection.stream` 返回`IStreamResult`其中包含`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-132">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="3d6f1-133">传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以获取来自通知`stream`调用。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-133">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3d6f1-134">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-134">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3d6f1-135">若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-135">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="3d6f1-136">调用此方法将导致`CancellationToken`（如果提供） 的集线器方法的参数以取消。</span><span class="sxs-lookup"><span data-stu-id="3d6f1-136">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="3d6f1-137">相关资源</span><span class="sxs-lookup"><span data-stu-id="3d6f1-137">Related resources</span></span>

* [<span data-ttu-id="3d6f1-138">中心</span><span class="sxs-lookup"><span data-stu-id="3d6f1-138">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3d6f1-139">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="3d6f1-139">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3d6f1-140">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="3d6f1-140">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="3d6f1-141">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="3d6f1-141">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
