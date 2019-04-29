---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何进行流式处理客户端和服务器之间的数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: d185056d3bdda089eaa46ae9b8e13ab7a4354f93
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165071"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>使用 ASP.NET Core SignalR 中流式处理

通过[brennan，第 Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR 支持流式处理从客户端到服务器和服务器到客户端。 这是适用于方案的数据片段随着时间的推移到达的位置。 当流式处理，每个片段是发送到客户端或服务器变得可用，而不是等待所有的数据变得可用。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR 支持流式处理服务器方法的返回值。 这是适用于方案的数据片段随着时间的推移到达的位置。 当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。

::: moniker-end

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="set-up-a-hub-for-streaming"></a>设置为流式处理中心

::: moniker range=">= aspnetcore-3.0"

集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Threading.Channels.ChannelReader%601>， `IAsyncEnumerable<T>`， `Task<ChannelReader<T>>`，或`Task<IAsyncEnumerable<T>>`。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

集线器方法自动成为流式处理的集线器方法时它将返回<xref:System.Threading.Channels.ChannelReader%601>或`Task<ChannelReader<T>>`。

::: moniker-end

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

::: moniker range=">= aspnetcore-3.0"

流式处理集线器方法可以返回`IAsyncEnumerable<T>`除了`ChannelReader<T>`。 若要返回的最简单方法`IAsyncEnumerable<T>`是使集线器方法的异步迭代器方法，如以下示例所示。 中心异步迭代器方法可接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。 异步迭代器方法避免通道，常见的问题，如不返回`ChannelReader`足够早或未完成的情况下退出方法<xref:System.Threading.Channels.ChannelWriter`1>。

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

下面的示例显示了数据流式传输到客户端使用通道的基础知识。 每当将对象写入到<xref:System.Threading.Channels.ChannelWriter%601>，该对象将立即发送给客户端。 在结束时，`ChannelWriter`完成告诉客户端流已关闭。

> [!NOTE]
> 写入`ChannelWriter<T>`在后台线程并返回`ChannelReader`越早越好。 其他集线器调用被阻止，直到`ChannelReader`返回。
>
> 包装中的逻辑`try ... catch`。 完成`Channel`中`catch`和外部`catch`以确保集线器方法调用是否正确完成。

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

服务器到客户端流式处理的集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，会触发的参数。 使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>客户端到服务器流式处理

当它接受一个或多个中心方法将自动成为客户端-服务器流式处理集线器方法<xref:System.Threading.Channels.ChannelReader`1>s。 下面的示例演示读取从客户端发送的流式处理数据的基础知识。 每当客户端写入<xref:System.Threading.Channels.ChannelWriter`1>，将数据写入到`ChannelReader`集线器方法读取从服务器上。

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a>.NET 客户端

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

`StreamAsChannelAsync`方法`HubConnection`用于调用服务器到客户端的流式处理方法。 将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。 上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。 一个`ChannelReader<T>`流调用中返回，并且表示在客户端上的流。

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

### <a name="client-to-server-streaming"></a>客户端到服务器流式处理

若要调用客户端-服务器流式处理集线器方法从.NET 客户端，创建`Channel`并传递`ChannelReader`的参数作为`SendAsync`， `InvokeAsync`，或`StreamAsChannelAsync`，取决于集线器方法调用。

每当将数据写入到`ChannelWriter`，在服务器上的集线器方法从客户端接收的数据的新项。

若要结束该流，请完成与通道`channel.Writer.Complete()`。

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

JavaScript 客户端上使用的集线器调用服务器到客户端流式处理方法`connection.stream`。 `stream`方法接受两个参数：

* 集线器方法的名称。 在以下示例中，中心方法名称是`Counter`。
* 在集线器方法中定义的参数。 在下面的示例中的参数是要接收的流项和流项之间的延迟的数量的计数。

`connection.stream` 返回`IStreamResult`，其中包含`subscribe`方法。 传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以接收来自通知`stream`调用。

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。 调用此方法可导致取消`CancellationToken`集线器方法，如果您提供了一个参数。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>客户端到服务器流式处理

通过传入对中心 JavaScript 客户端调用客户端-服务器流式处理方法`Subject`作为参数`send`， `invoke`，或`stream`，取决于集线器方法调用。 `Subject`是一个类，如下所示`Subject`。 例如在 RxJS 中，您可以使用[使用者](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)该库中的类。

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

调用`subject.next(item)`与某项的项写入流，和集线器方法接收服务器上的项。

若要结束该流，请调用`subject.complete()`。

## <a name="java-client"></a>Java 客户端

### <a name="server-to-client-streaming"></a>服务器到客户端流式处理

SignalR Java 客户端使用`stream`方法调用流式处理方法。 `stream` 接受三个或多个参数：

* 流项的预期的类型。
* 集线器方法的名称。
* 在集线器方法中定义的参数。

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`方法`HubConnection`返回流项类型的可观察量。 可观察的类型`subscribe`方法是，在哪里`onNext`，`onError`和`onCompleted`定义处理程序。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
