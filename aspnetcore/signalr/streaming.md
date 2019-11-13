---
title: 在 ASP.NET Core 中使用流式处理 SignalR
author: bradygaster
description: 了解如何在客户端和服务器之间流式传输数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 7825beba55cefb6236fd8d8e332d030a7e4fc6df
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963889"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a>在 ASP.NET Core 中使用流式处理 SignalR

作者： [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR 支持从客户端传输到服务器以及从服务器传输到客户端。 这适用于数据片段随着时间的推移而发生的情况。 流式传输时，每个片段一旦变为可用，就会发送到客户端或服务器，而不是等待所有数据都可用。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR 支持服务器方法的流返回值。 这适用于数据片段随着时间的推移而发生的情况。 将返回值流式传输到客户端时，每个片段会在其可用时立即发送到客户端，而不是等待所有数据都可用。

::: moniker-end

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="set-up-a-hub-for-streaming"></a>设置用于流式传输的集线器

::: moniker range=">= aspnetcore-3.0"

当集线器方法返回 <xref:System.Collections.Generic.IAsyncEnumerable`1>、<xref:System.Threading.Channels.ChannelReader%601>、`Task<IAsyncEnumerable<T>>`或 `Task<ChannelReader<T>>`时，它会自动变为流式处理中心方法。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

当集线器方法返回 <xref:System.Threading.Channels.ChannelReader%601> 或 `Task<ChannelReader<T>>`时，该方法会自动变为流式处理中心方法。

::: moniker-end

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

::: moniker range=">= aspnetcore-3.0"

除了 `ChannelReader<T>`之外，流式处理中心方法还可以返回 `IAsyncEnumerable<T>`。 返回 `IAsyncEnumerable<T>` 的最简单方法是将集线器方法设为异步迭代器方法，如下例所示。 中心异步迭代器方法可以接受当客户端从流中取消订阅时触发的 `CancellationToken` 参数。 异步迭代器方法避免了与通道常见的问题，例如，不能尽早返回 `ChannelReader` 或退出方法，无需完成 <xref:System.Threading.Channels.ChannelWriter`1>。

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

下面的示例演示了使用通道将数据流式传输到客户端的基础知识。 每当将对象写入 <xref:System.Threading.Channels.ChannelWriter%601>时，都会立即将对象发送到客户端。 最后，`ChannelWriter` 完成，告诉客户端流已关闭。

> [!NOTE]
> 在后台线程上写入 `ChannelWriter<T>`，并尽快返回 `ChannelReader`。 其他中心调用会被阻止，直到返回 `ChannelReader`。
>
> `try ... catch`中的环绕逻辑。 完成 `catch` 和 `catch` 之外的 `Channel`，确保中心方法调用正确完成。

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

服务器到客户端流式处理中心方法可以接受当客户端从流中取消订阅时触发的 `CancellationToken` 参数。 如果客户端在流末尾之前断开连接，请使用此标记停止服务器操作并释放任何资源。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>客户端到服务器的流式处理

当某个集线器方法接受 <xref:System.Threading.Channels.ChannelReader%601> 或 <xref:System.Collections.Generic.IAsyncEnumerable%601>类型的一个或多个对象时，它会自动成为客户端到服务器的流式处理中心方法。 下面的示例演示了读取从客户端发送的流式处理数据的基础知识。 每当客户端写入 <xref:System.Threading.Channels.ChannelWriter%601>时，数据都会写入中心方法读取的服务器上的 `ChannelReader` 中。

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

下面是方法的 <xref:System.Collections.Generic.IAsyncEnumerable%601> 版本。

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

## <a name="net-client"></a>.NET 客户端

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理


::: moniker range=">= aspnetcore-3.0"

`HubConnection` 上的 `StreamAsync` 和 `StreamAsChannelAsync` 方法用于调用服务器到客户端的流式处理方法。 将中心方法中定义的集线器方法名称和参数传递到 `StreamAsync` 或 `StreamAsChannelAsync`。 `StreamAsync<T>` 和 `StreamAsChannelAsync<T>` 上的泛型参数指定流方法返回的对象的类型。 `IAsyncEnumerable<T>` 或 `ChannelReader<T>` 类型的对象从流调用返回，并表示客户端上的流。

返回 `IAsyncEnumerable<int>`的 `StreamAsync` 示例：

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

一个返回 `ChannelReader<int>`的相应 `StreamAsChannelAsync` 示例：

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

`HubConnection` 上的 `StreamAsChannelAsync` 方法用于调用服务器到客户端流式处理方法。 将中心方法中定义的集线器方法名称和参数传递到 `StreamAsChannelAsync`。 `StreamAsChannelAsync<T>` 上的泛型参数指定流方法返回的对象的类型。 从流调用返回 `ChannelReader<T>`，并表示客户端上的流。

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

`HubConnection` 上的 `StreamAsChannelAsync` 方法用于调用服务器到客户端流式处理方法。 将中心方法中定义的集线器方法名称和参数传递到 `StreamAsChannelAsync`。 `StreamAsChannelAsync<T>` 上的泛型参数指定流方法返回的对象的类型。 从流调用返回 `ChannelReader<T>`，并表示客户端上的流。

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

### <a name="client-to-server-streaming"></a>客户端到服务器的流式处理

可以通过两种方法从 .NET 客户端调用客户端到服务器的流式处理中心方法。 可以将 `IAsyncEnumerable<T>` 或 `ChannelReader` 作为参数传入 `SendAsync`、`InvokeAsync`或 `StreamAsChannelAsync`，具体取决于所调用的集线器方法。

只要将数据写入 `IAsyncEnumerable` 或 `ChannelWriter` 对象，服务器上的集线器方法就会收到来自客户端的数据的新项。

如果使用 `IAsyncEnumerable` 对象，则流在返回流项的方法退出后结束。

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

或者，如果使用的是 `ChannelWriter`，则使用 `channel.Writer.Complete()`完成通道：

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript 客户端

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

JavaScript 客户端通过 `connection.stream`调用集线器上的服务器到客户端流式处理方法。 `stream` 方法接受两个参数：

* 集线器方法的名称。 在下面的示例中，中心方法名称是 `Counter`。
* 在 hub 方法中定义的参数。 在下面的示例中，参数是要接收的流项数的计数以及流项之间的延迟。

`connection.stream` 返回 `IStreamResult`，它包含 `subscribe` 方法。 将 `IStreamSubscriber` 传递到 `subscribe`，并设置 `next`、`error`和 `complete` 回调，以便从 `stream` 调用接收通知。

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

若要从客户端结束流，请对从 `subscribe` 方法返回的 `ISubscription` 调用 `dispose` 方法。 调用此方法会导致取消集线器方法的 `CancellationToken` 参数（如果提供了一个参数）。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

若要从客户端结束流，请对从 `subscribe` 方法返回的 `ISubscription` 调用 `dispose` 方法。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>客户端到服务器的流式处理

JavaScript 客户端通过将 `Subject` 作为参数传入到 `send`、`invoke`或 `stream`（具体取决于所调用的集线器方法），在集线器上调用客户端到服务器的流式处理方法。 `Subject` 是一种类似于 `Subject`的类。 例如，在 RxJS 中，可以使用该库中的[Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)类。

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

使用项调用 `subject.next(item)` 会将项写入流，集线器方法接收服务器上的项。

若要结束流，请调用 `subject.complete()`。

## <a name="java-client"></a>Java 客户端

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

SignalR Java 客户端使用 `stream` 方法来调用流式处理方法。 `stream` 接受三个或更多参数：

* 流项的预期类型。
* 集线器方法的名称。
* 在 hub 方法中定义的参数。

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`HubConnection` 上的 `stream` 方法返回流项类型的可观察对象。 可观察类型的 `subscribe` 方法是定义 `onNext`、`onError` 和 `onCompleted` 处理程序的位置。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
