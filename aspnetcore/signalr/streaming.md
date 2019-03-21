---
title: 使用 ASP.NET Core SignalR 中流式处理
author: bradygaster
description: 了解如何从服务器集线器方法返回的值的流和使用使用.NET 和 JavaScript 客户端的流。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: 7c176e3f21ffca7b97d9d3c2e8861032f22587b8
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264308"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>使用 ASP.NET Core SignalR 中流式处理

通过[brennan，第 Conroy](https://github.com/BrennanConroy)

ASP.NET Core SignalR 支持流式处理服务器方法的返回值。 这是适用于其中的数据片段会随时间的方案。 当返回值流式传输到客户端时，每个片段是发送到客户端变得可用，而非等待所有的数据变得可用。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="set-up-the-hub"></a>设置中心

::: moniker range=">= aspnetcore-3.0"

集线器方法自动成为流式处理的集线器方法时它将返回`ChannelReader<T>`， `IAsyncEnumerable<T>`， `Task<ChannelReader<T>>`，或`Task<IAsyncEnumerable<T>>`。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

集线器方法自动成为流式处理的集线器方法时它将返回`ChannelReader<T>`或`Task<ChannelReader<T>>`。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET Core 3.0 或更高版本，流式处理集线器方法可以返回`IAsyncEnumerable<T>`除了`ChannelReader<T>`。 若要返回的最简单方法`IAsyncEnumerable<T>`是使集线器方法的异步迭代器方法，如以下示例所示。 中心异步迭代器方法可接受`CancellationToken`从流的客户端取消订阅时，将触发的参数。 异步迭代器方法轻松地避免出现频道常见的问题，如不返回`ChannelReader`足够早或未完成的情况下退出方法`ChannelWriter`。

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

下面的示例显示了数据流式传输到客户端使用通道的基础知识。 每当将对象写入到`ChannelWriter`该对象将立即发送给客户端。 在结束时，`ChannelWriter`完成告诉客户端流已关闭。

> [!NOTE]
> * 写入`ChannelWriter`在后台线程并返回`ChannelReader`越早越好。 将阻止其他集线器调用，直到`ChannelReader`返回。
> * 包装中的逻辑`try ... catch`并完成`Channel`catch 和外部的关键点，以确保在中心正确完成方法调用。

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

在 ASP.NET Core 2.2 或更高版本，流式处理集线器方法可以接受`CancellationToken`从流的客户端取消订阅时，将触发的参数。 使用此令牌来停止服务器操作和释放任何资源，如果客户端断开连接之前流的末尾。

::: moniker-end

## <a name="net-client"></a>.NET 客户端

`StreamAsChannelAsync`方法`HubConnection`用于调用流式处理方法。 将中心方法名称和到中心方法中定义的自变量传递`StreamAsChannelAsync`。 上的泛型参数`StreamAsChannelAsync<T>`指定流式处理方法返回的对象的类型。 一个`ChannelReader<T>`返回从流调用，并且表示在客户端上的流。 若要读取数据，常见模式是循环访问`WaitToReadAsync`，并调用`TryRead`数据时可用。 循环将结束时该流已关闭服务器，或取消标记传递给`StreamAsChannelAsync`被取消。

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

## <a name="javascript-client"></a>JavaScript 客户端

JavaScript 客户端调用集线器上流式处理方法使用`connection.stream`。 `stream`方法接受两个参数：

* 集线器方法的名称。 在以下示例中，中心方法名称是`Counter`。
* 在集线器方法中定义的参数。 在以下示例中，参数是： 若要接收的流项和流项之间的延迟的数量的计数。

`connection.stream` 返回`IStreamResult`其中包含`subscribe`方法。 传递`IStreamSubscriber`到`subscribe`并设置`next`， `error`，并`complete`回调以获取来自通知`stream`调用。

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

若要结束从客户端的流，请调用`dispose`方法`ISubscription`从返回`subscribe`方法。 调用此方法将导致`CancellationToken`（如果提供） 的集线器方法的参数以取消。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="java-client"></a>Java 客户端

SignalR Java 客户端使用`stream`方法调用流式处理方法。 它接受三个或多个参数：

* 流项的预期的类型
* 集线器方法的名称。
* 在集线器方法中定义的参数。

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`方法`HubConnection`返回流项类型的可观察量。 可观察的类型`subscribe`方法是在其中定义你`onNext`，`onError`和`onCompleted`处理程序。

::: moniker-end

## <a name="related-resources"></a>相关资源

* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
