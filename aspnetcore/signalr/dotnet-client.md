---
title: ASP.NET Core SignalR.NET 客户端
author: bradygaster
description: 有关 ASP.NET Core SignalR.NET 客户端信息
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 97c553874cb1e4b678fa0e5cd65074f135193861
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153117"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="38a61-103">ASP.NET Core SignalR.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="38a61-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="38a61-104">ASP.NET Core SignalR.NET 客户端库可让你与 SignalR 集线器从.NET 应用程序进行通信。</span><span class="sxs-lookup"><span data-stu-id="38a61-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="38a61-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="38a61-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="38a61-106">这篇文章中的代码示例是使用 ASP.NET Core SignalR.NET 客户端的 WPF 应用。</span><span class="sxs-lookup"><span data-stu-id="38a61-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="38a61-107">安装 SignalR.NET 客户端包</span><span class="sxs-lookup"><span data-stu-id="38a61-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="38a61-108">`Microsoft.AspNetCore.SignalR.Client`包所需的.NET 客户端连接到 SignalR 集线器。</span><span class="sxs-lookup"><span data-stu-id="38a61-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="38a61-109">若要安装客户端库，请运行以下命令**程序包管理器控制台**窗口：</span><span class="sxs-lookup"><span data-stu-id="38a61-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="38a61-110">连接到中心</span><span class="sxs-lookup"><span data-stu-id="38a61-110">Connect to a hub</span></span>

<span data-ttu-id="38a61-111">若要建立连接，创建`HubConnectionBuilder`，并调用`Build`。</span><span class="sxs-lookup"><span data-stu-id="38a61-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="38a61-112">生成一个连接时，可以配置中心 URL、 协议、 传输类型、 日志级别、 标头，和其他选项。</span><span class="sxs-lookup"><span data-stu-id="38a61-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="38a61-113">配置任何必需的选项的方法是插入的任何`HubConnectionBuilder`方法转换`Build`。</span><span class="sxs-lookup"><span data-stu-id="38a61-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="38a61-114">启动与连接`StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="38a61-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="38a61-115">已断开连接的句柄</span><span class="sxs-lookup"><span data-stu-id="38a61-115">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="38a61-116">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="38a61-116">Automatically reconnect</span></span>

<span data-ttu-id="38a61-117"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>可以配置为自动重新连接，使用`WithAutomaticReconnect`方法<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>。</span><span class="sxs-lookup"><span data-stu-id="38a61-117">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="38a61-118">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-118">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="38a61-119">不带任何参数，`WithAutomaticReconnect()`配置客户端等待分别之前尝试每次重新连接尝试后四个失败的尝试, 停止 0、 2、 10 和 30 秒。</span><span class="sxs-lookup"><span data-stu-id="38a61-119">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="38a61-120">在开始任何重新连接尝试之前,`HubConnection`将转换到`HubConnectionState.Reconnecting`状态，并触发`Reconnecting`事件。</span><span class="sxs-lookup"><span data-stu-id="38a61-120">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="38a61-121">这提供了机会来警告用户该连接已丢失，并禁用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="38a61-121">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="38a61-122">非交互式应用程序可以启动队列或删除消息。</span><span class="sxs-lookup"><span data-stu-id="38a61-122">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="38a61-123">如果客户端成功地重新连接在其前四个尝试`HubConnection`将转换回`Connected`状态，并触发`Reconnected`事件。</span><span class="sxs-lookup"><span data-stu-id="38a61-123">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="38a61-124">这提供机会通知，告知用户重新建立连接，并且取消所有排队的消息的排队。</span><span class="sxs-lookup"><span data-stu-id="38a61-124">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="38a61-125">由于连接到服务器，完全新查找新`ConnectionId`将提供给`Reconnected`事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="38a61-125">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="38a61-126">`Reconnected`事件处理程序`connectionId`参数为 null 如果`HubConnection`配置为[跳过协商](xref:signalr/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="38a61-126">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="38a61-127">`WithAutomaticReconnect()` 不会配置`HubConnection`重试初始启动时失败，因此需要手动处理发生启动失败：</span><span class="sxs-lookup"><span data-stu-id="38a61-127">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="38a61-128">如果客户端不会成功地重新连接在其前四个尝试`HubConnection`将转换到`Disconnected`状态，并触发<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件。</span><span class="sxs-lookup"><span data-stu-id="38a61-128">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="38a61-129">这提供机会来尝试手动重新启动连接或通知用户的连接已永久丢失。</span><span class="sxs-lookup"><span data-stu-id="38a61-129">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="38a61-130">若要配置自定义多个断开连接之前重新连接尝试，或更改重新连接时间`WithAutomaticReconnect`接受数字表示以毫秒为单位的延迟开始每次重新连接尝试前等待的数组。</span><span class="sxs-lookup"><span data-stu-id="38a61-130">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="38a61-131">前面的示例配置`HubConnection`启动会断开连接后立即尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-131">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="38a61-132">这也是默认配置，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="38a61-132">This is also true for the default configuration.</span></span>

<span data-ttu-id="38a61-133">如果第一次重新连接尝试失败，第二次重新连接尝试将还立即开始而不是等待 2 秒像默认配置。</span><span class="sxs-lookup"><span data-stu-id="38a61-133">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="38a61-134">如果第二次重新连接尝试失败，第三次重新连接尝试会在 10 秒内再次就像默认配置。</span><span class="sxs-lookup"><span data-stu-id="38a61-134">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="38a61-135">自定义行为然后分离时再次从默认行为通过第三个重新连接尝试失败后停止。</span><span class="sxs-lookup"><span data-stu-id="38a61-135">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="38a61-136">在默认配置中将是一个多重新连接尝试在另一个 30 秒内。</span><span class="sxs-lookup"><span data-stu-id="38a61-136">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="38a61-137">如果你想甚至更好地控制时间安排和数量自动重新连接尝试`WithAutomaticReconnect`接受一个对象，实现`IRetryPolicy`接口，它具有一个名为的单个方法`NextRetryDelay`。</span><span class="sxs-lookup"><span data-stu-id="38a61-137">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="38a61-138">`NextRetryDelay` 使用类型采用单个参数`RetryContext`。</span><span class="sxs-lookup"><span data-stu-id="38a61-138">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="38a61-139">`RetryContext`有三个属性： `PreviousRetryCount`，`ElapsedTime`并`RetryReason`哪些`long`、 一个`TimeSpan`和`Exception`分别。</span><span class="sxs-lookup"><span data-stu-id="38a61-139">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="38a61-140">第一次重新连接尝试前, 两者`PreviousRetryCount`和`ElapsedTime`将为零，和`RetryReason`将导致连接丢失的异常。</span><span class="sxs-lookup"><span data-stu-id="38a61-140">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="38a61-141">每个失败的重试尝试后,`PreviousRetryCount`将加 1，`ElapsedTime`随即更新以反映到目前为止，重新连接所用的时间量和`RetryReason`将导致最后一次的重新连接尝试失败的异常。</span><span class="sxs-lookup"><span data-stu-id="38a61-141">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="38a61-142">`NextRetryDelay` 必须返回任一一个 timespan，用于表示的时间等待下一次重新连接尝试或`null`如果`HubConnection`应停止重新连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-142">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="38a61-143">或者，可以编写代码，如中所示手动将重新连接的客户端[手动重新连接](#manually-reconnect)。</span><span class="sxs-lookup"><span data-stu-id="38a61-143">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="38a61-144">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="38a61-144">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="38a61-145">在 3.0 之前，SignalR.NET 客户端不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-145">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="38a61-146">必须编写代码将手动重新连接你的客户端。</span><span class="sxs-lookup"><span data-stu-id="38a61-146">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="38a61-147">使用<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件响应丢失连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-147">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="38a61-148">例如，你可能想要自动执行重新连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-148">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="38a61-149">`Closed`事件需要返回的委托`Task`，它允许异步代码运行，而不使用`async void`。</span><span class="sxs-lookup"><span data-stu-id="38a61-149">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="38a61-150">若要满足中的委托签名`Closed`运行以同步方式，返回的事件处理程序`Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="38a61-150">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="38a61-151">异步支持的主要原因是使您可以重新启动连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-151">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="38a61-152">正在启动的连接是一个异步操作。</span><span class="sxs-lookup"><span data-stu-id="38a61-152">Starting a connection is an async action.</span></span>

<span data-ttu-id="38a61-153">在`Closed`处理程序重新启动该连接，请考虑等待某些随机延迟，以防止服务器，重载，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="38a61-153">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="38a61-154">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="38a61-154">Call hub methods from client</span></span>

<span data-ttu-id="38a61-155">`InvokeAsync` 在该中心将调用方法。</span><span class="sxs-lookup"><span data-stu-id="38a61-155">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="38a61-156">将中心方法名称和到中心方法中定义的任何参数传递`InvokeAsync`。</span><span class="sxs-lookup"><span data-stu-id="38a61-156">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="38a61-157">SignalR 是异步的因此请使用`async`和`await`时进行调用。</span><span class="sxs-lookup"><span data-stu-id="38a61-157">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="38a61-158">`InvokeAsync`方法将返回`Task`服务器方法返回时完成。</span><span class="sxs-lookup"><span data-stu-id="38a61-158">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="38a61-159">返回值，如果有的话，提供的结果作为`Task`。</span><span class="sxs-lookup"><span data-stu-id="38a61-159">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="38a61-160">在服务器上的方法引发的任何异常生成出错`Task`。</span><span class="sxs-lookup"><span data-stu-id="38a61-160">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="38a61-161">使用`await`要等待的时间才能完成的服务器方法的语法和`try...catch`语法来处理错误。</span><span class="sxs-lookup"><span data-stu-id="38a61-161">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="38a61-162">`SendAsync`方法将返回`Task`并完成时该消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="38a61-162">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="38a61-163">由于这未提供任何返回值`Task`不等待，直到服务器方法完成。</span><span class="sxs-lookup"><span data-stu-id="38a61-163">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="38a61-164">在发送消息时在客户端上引发的任何异常生成出错`Task`。</span><span class="sxs-lookup"><span data-stu-id="38a61-164">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="38a61-165">使用`await`和`try...catch`语法来处理发送错误。</span><span class="sxs-lookup"><span data-stu-id="38a61-165">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="38a61-166">如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="38a61-166">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="38a61-167">有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="38a61-167">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="38a61-168">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="38a61-168">Call client methods from hub</span></span>

<span data-ttu-id="38a61-169">定义使用集线器调用的方法`connection.On`之后生成，但之前启动连接。</span><span class="sxs-lookup"><span data-stu-id="38a61-169">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="38a61-170">在前面的代码`connection.On`运行时服务器端代码将调用它使用`SendAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="38a61-170">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="38a61-171">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="38a61-171">Error handling and logging</span></span>

<span data-ttu-id="38a61-172">处理 try catch 语句的错误。</span><span class="sxs-lookup"><span data-stu-id="38a61-172">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="38a61-173">检查`Exception`对象，以确定要执行后发生错误的适当操作。</span><span class="sxs-lookup"><span data-stu-id="38a61-173">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="38a61-174">其他资源</span><span class="sxs-lookup"><span data-stu-id="38a61-174">Additional resources</span></span>

* [<span data-ttu-id="38a61-175">中心</span><span class="sxs-lookup"><span data-stu-id="38a61-175">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="38a61-176">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="38a61-176">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="38a61-177">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="38a61-177">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="38a61-178">Azure SignalR 服务无服务器文档</span><span class="sxs-lookup"><span data-stu-id="38a61-178">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
