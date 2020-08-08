---
title: 使用 ASP.NET Core 中的流式处理SignalR
author: bradygaster
description: 了解如何在客户端和服务器之间流式传输数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: e0eabe711fd69e42bd9bfa5e03a92e1df780e4db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022506"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="eab35-103">使用 ASP.NET Core 中的流式处理SignalR</span><span class="sxs-lookup"><span data-stu-id="eab35-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="eab35-104">作者： [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="eab35-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eab35-105">ASP.NET Core SignalR 支持从客户端到服务器以及从服务器到客户端的流式传输。</span><span class="sxs-lookup"><span data-stu-id="eab35-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="eab35-106">这适用于数据片段随着时间的推移而发生的情况。</span><span class="sxs-lookup"><span data-stu-id="eab35-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="eab35-107">流式传输时，每个片段一旦变为可用，就会发送到客户端或服务器，而不是等待所有数据都可用。</span><span class="sxs-lookup"><span data-stu-id="eab35-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eab35-108">ASP.NET Core SignalR 支持服务器方法的流返回值。</span><span class="sxs-lookup"><span data-stu-id="eab35-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="eab35-109">这适用于数据片段随着时间的推移而发生的情况。</span><span class="sxs-lookup"><span data-stu-id="eab35-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="eab35-110">将返回值流式传输到客户端时，每个片段会在其可用时立即发送到客户端，而不是等待所有数据都可用。</span><span class="sxs-lookup"><span data-stu-id="eab35-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="eab35-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eab35-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="eab35-112">设置用于流式传输的集线器</span><span class="sxs-lookup"><span data-stu-id="eab35-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eab35-113">当集线器方法返回、、或时，它会自动成为流式处理中心方法 <xref:System.Collections.Generic.IAsyncEnumerable`1> <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eab35-114">当集线器方法返回或时，它会自动成为流式处理中心方法 <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="eab35-115">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eab35-116">除了之外，流集线器方法还可以返回 `IAsyncEnumerable<T>` `ChannelReader<T>` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="eab35-117">返回的最简单方法 `IAsyncEnumerable<T>` 是将集线器方法设为异步迭代器方法，如下例所示。</span><span class="sxs-lookup"><span data-stu-id="eab35-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="eab35-118">中心异步迭代器方法可以接受 `CancellationToken` 当客户端从流中取消订阅时触发的参数。</span><span class="sxs-lookup"><span data-stu-id="eab35-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="eab35-119">异步迭代器方法避免了与通道常见的问题，例如，在没有 `ChannelReader` 完成的情况下，不能提前返回或退出方法 <xref:System.Threading.Channels.ChannelWriter`1> 。</span><span class="sxs-lookup"><span data-stu-id="eab35-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="eab35-120">下面的示例演示了使用通道将数据流式传输到客户端的基础知识。</span><span class="sxs-lookup"><span data-stu-id="eab35-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="eab35-121">每当将对象写入到时 <xref:System.Threading.Channels.ChannelWriter%601> ，都会立即将对象发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="eab35-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="eab35-122">结束时，已 `ChannelWriter` 完成，告诉客户端流已关闭。</span><span class="sxs-lookup"><span data-stu-id="eab35-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="eab35-123">`ChannelWriter<T>`在后台线程上写入，并尽快返回 `ChannelReader` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="eab35-124">在返回之前，其他中心调用会被阻止 `ChannelReader` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="eab35-125">在中环绕逻辑 `try ... catch` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="eab35-126">完成 `Channel` 中 `catch` 和之外的， `catch` 以确保中心方法调用正确完成。</span><span class="sxs-lookup"><span data-stu-id="eab35-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="eab35-127">服务器到客户端流式处理中心方法可以接受 `CancellationToken` 当客户端从流中取消订阅时触发的参数。</span><span class="sxs-lookup"><span data-stu-id="eab35-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="eab35-128">如果客户端在流末尾之前断开连接，请使用此标记停止服务器操作并释放任何资源。</span><span class="sxs-lookup"><span data-stu-id="eab35-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="eab35-129">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-129">Client-to-server streaming</span></span>

<span data-ttu-id="eab35-130">当某个集线器方法接受一个或多个类型为或的对象时，它会自动成为客户端到服务器的流式处理中心方法 <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> 。</span><span class="sxs-lookup"><span data-stu-id="eab35-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="eab35-131">下面的示例演示了读取从客户端发送的流式处理数据的基础知识。</span><span class="sxs-lookup"><span data-stu-id="eab35-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="eab35-132">每当客户端向中写入 <xref:System.Threading.Channels.ChannelWriter%601> 数据时，数据就会写入 `ChannelReader` 中心方法所读取的服务器上的。</span><span class="sxs-lookup"><span data-stu-id="eab35-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="eab35-133"><xref:System.Collections.Generic.IAsyncEnumerable%601>下面是方法的版本。</span><span class="sxs-lookup"><span data-stu-id="eab35-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="eab35-134">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="eab35-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="eab35-135">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eab35-136">`StreamAsync`上的和 `StreamAsChannelAsync` 方法 `HubConnection` 用于调用服务器到客户端的流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="eab35-137">将 hub 方法中定义的集线器方法名称和参数传递给 `StreamAsync` 或 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="eab35-138">和上的泛型 `StreamAsync<T>` 参数 `StreamAsChannelAsync<T>` 指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="eab35-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="eab35-139">类型 `IAsyncEnumerable<T>` 为或的对象 `ChannelReader<T>` 将从流调用返回，并表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="eab35-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="eab35-140">`StreamAsync`返回 `IAsyncEnumerable<int>` 以下内容的示例：</span><span class="sxs-lookup"><span data-stu-id="eab35-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="eab35-141">返回的相应 `StreamAsChannelAsync` 示例 `ChannelReader<int>` ：</span><span class="sxs-lookup"><span data-stu-id="eab35-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="eab35-142">`StreamAsChannelAsync`上的方法 `HubConnection` 用于调用服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="eab35-143">将中心方法中定义的集线器方法名称和参数传递给 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="eab35-144">上的泛型参数 `StreamAsChannelAsync<T>` 指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="eab35-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="eab35-145">`ChannelReader<T>`从流调用返回，表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="eab35-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="eab35-146">`StreamAsChannelAsync`上的方法 `HubConnection` 用于调用服务器到客户端流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="eab35-147">将中心方法中定义的集线器方法名称和参数传递给 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="eab35-148">上的泛型参数 `StreamAsChannelAsync<T>` 指定流方法返回的对象的类型。</span><span class="sxs-lookup"><span data-stu-id="eab35-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="eab35-149">`ChannelReader<T>`从流调用返回，表示客户端上的流。</span><span class="sxs-lookup"><span data-stu-id="eab35-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="eab35-150">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-150">Client-to-server streaming</span></span>

<span data-ttu-id="eab35-151">可以通过两种方法从 .NET 客户端调用客户端到服务器的流式处理中心方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="eab35-152">可以将 `IAsyncEnumerable<T>` 或 `ChannelReader` 作为参数传入 `SendAsync` 、 `InvokeAsync` 或 `StreamAsChannelAsync` ，具体取决于所调用的中心方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="eab35-153">只要将数据写入到 `IAsyncEnumerable` 或 `ChannelWriter` 对象，服务器上的集线器方法就会收到来自客户端的数据的新项。</span><span class="sxs-lookup"><span data-stu-id="eab35-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="eab35-154">如果使用 `IAsyncEnumerable` 对象，则流在返回流项的方法退出后结束。</span><span class="sxs-lookup"><span data-stu-id="eab35-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="eab35-155">或者，如果使用的是 `ChannelWriter` ，请使用 `channel.Writer.Complete()` 以下内容完成通道：</span><span class="sxs-lookup"><span data-stu-id="eab35-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="eab35-156">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="eab35-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="eab35-157">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-157">Server-to-client streaming</span></span>

<span data-ttu-id="eab35-158">JavaScript 客户端通过调用与的集线器上的服务器到客户端流式处理方法 `connection.stream` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="eab35-159">此 `stream` 方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="eab35-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="eab35-160">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="eab35-160">The name of the hub method.</span></span> <span data-ttu-id="eab35-161">在下面的示例中，中心方法名称是 `Counter` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="eab35-162">在 hub 方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="eab35-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="eab35-163">在下面的示例中，参数是要接收的流项数的计数以及流项之间的延迟。</span><span class="sxs-lookup"><span data-stu-id="eab35-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="eab35-164">`connection.stream`返回一个 `IStreamResult` ，它包含 `subscribe` 方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="eab35-165">向传递 `IStreamSubscriber` ， `subscribe` 并设置 `next` 、 `error` 和回调， `complete` 以接收来自调用的通知 `stream` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="eab35-166">若要从客户端结束流，请 `dispose` 对 `ISubscription` 从方法返回的调用方法 `subscribe` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="eab35-167">如果提供了，则调用此方法会导致取消 `CancellationToken` 集线器方法的参数。</span><span class="sxs-lookup"><span data-stu-id="eab35-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="eab35-168">若要从客户端结束流，请 `dispose` 对 `ISubscription` 从方法返回的调用方法 `subscribe` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="eab35-169">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-169">Client-to-server streaming</span></span>

<span data-ttu-id="eab35-170">JavaScript 客户端通过将作为自变量传入、或，来调用集线器上的客户端到服务器流式处理方法， `Subject` `send` `invoke` `stream` 具体取决于所调用的集线器方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="eab35-171">`Subject`是一个类似于的类 `Subject` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="eab35-172">例如，在 RxJS 中，可以使用该库中的[Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)类。</span><span class="sxs-lookup"><span data-stu-id="eab35-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="eab35-173">使用项调用会将 `subject.next(item)` 项写入流，集线器方法接收服务器上的项。</span><span class="sxs-lookup"><span data-stu-id="eab35-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="eab35-174">若要结束流，请调用 `subject.complete()` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="eab35-175">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="eab35-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="eab35-176">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="eab35-176">Server-to-client streaming</span></span>

<span data-ttu-id="eab35-177">SignalRJava 客户端使用 `stream` 方法来调用流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="eab35-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="eab35-178">`stream`接受三个或更多参数：</span><span class="sxs-lookup"><span data-stu-id="eab35-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="eab35-179">流项的预期类型。</span><span class="sxs-lookup"><span data-stu-id="eab35-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="eab35-180">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="eab35-180">The name of the hub method.</span></span>
* <span data-ttu-id="eab35-181">在 hub 方法中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="eab35-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="eab35-182">`stream`上的方法 `HubConnection` 返回流项类型的可观察对象。</span><span class="sxs-lookup"><span data-stu-id="eab35-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="eab35-183">可观察的类型的 `subscribe` 方法是 `onNext` 定义的位置 `onError` `onCompleted` 。</span><span class="sxs-lookup"><span data-stu-id="eab35-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="eab35-184">其他资源</span><span class="sxs-lookup"><span data-stu-id="eab35-184">Additional resources</span></span>

* [<span data-ttu-id="eab35-185">集线器</span><span class="sxs-lookup"><span data-stu-id="eab35-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="eab35-186">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="eab35-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eab35-187">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="eab35-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="eab35-188">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="eab35-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
