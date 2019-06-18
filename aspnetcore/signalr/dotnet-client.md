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
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR.NET 客户端

ASP.NET Core SignalR.NET 客户端库可让你与 SignalR 集线器从.NET 应用程序进行通信。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

这篇文章中的代码示例是使用 ASP.NET Core SignalR.NET 客户端的 WPF 应用。

## <a name="install-the-signalr-net-client-package"></a>安装 SignalR.NET 客户端包

`Microsoft.AspNetCore.SignalR.Client`包所需的.NET 客户端连接到 SignalR 集线器。 若要安装客户端库，请运行以下命令**程序包管理器控制台**窗口：

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>连接到中心

若要建立连接，创建`HubConnectionBuilder`，并调用`Build`。 生成一个连接时，可以配置中心 URL、 协议、 传输类型、 日志级别、 标头，和其他选项。 配置任何必需的选项的方法是插入的任何`HubConnectionBuilder`方法转换`Build`。 启动与连接`StartAsync`。

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>已断开连接的句柄

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>可以配置为自动重新连接，使用`WithAutomaticReconnect`方法<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>。 默认情况下，它不会自动重新连接。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

不带任何参数，`WithAutomaticReconnect()`配置客户端等待分别之前尝试每次重新连接尝试后四个失败的尝试, 停止 0、 2、 10 和 30 秒。

在开始任何重新连接尝试之前,`HubConnection`将转换到`HubConnectionState.Reconnecting`状态，并触发`Reconnecting`事件。  这提供了机会来警告用户该连接已丢失，并禁用 UI 元素。 非交互式应用程序可以启动队列或删除消息。

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

如果客户端成功地重新连接在其前四个尝试`HubConnection`将转换回`Connected`状态，并触发`Reconnected`事件。 这提供机会通知，告知用户重新建立连接，并且取消所有排队的消息的排队。

由于连接到服务器，完全新查找新`ConnectionId`将提供给`Reconnected`事件处理程序。

> [!WARNING]
> `Reconnected`事件处理程序`connectionId`参数为 null 如果`HubConnection`配置为[跳过协商](xref:signalr/configuration#configure-client-options)。

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` 不会配置`HubConnection`重试初始启动时失败，因此需要手动处理发生启动失败：

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

如果客户端不会成功地重新连接在其前四个尝试`HubConnection`将转换到`Disconnected`状态，并触发<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件。 这提供机会来尝试手动重新启动连接或通知用户的连接已永久丢失。

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

若要配置自定义多个断开连接之前重新连接尝试，或更改重新连接时间`WithAutomaticReconnect`接受数字表示以毫秒为单位的延迟开始每次重新连接尝试前等待的数组。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

前面的示例配置`HubConnection`启动会断开连接后立即尝试重新连接。 这也是默认配置，则返回 true。

如果第一次重新连接尝试失败，第二次重新连接尝试将还立即开始而不是等待 2 秒像默认配置。

如果第二次重新连接尝试失败，第三次重新连接尝试会在 10 秒内再次就像默认配置。

自定义行为然后分离时再次从默认行为通过第三个重新连接尝试失败后停止。 在默认配置中将是一个多重新连接尝试在另一个 30 秒内。

如果你想甚至更好地控制时间安排和数量自动重新连接尝试`WithAutomaticReconnect`接受一个对象，实现`IRetryPolicy`接口，它具有一个名为的单个方法`NextRetryDelay`。

`NextRetryDelay` 使用类型采用单个参数`RetryContext`。 `RetryContext`有三个属性： `PreviousRetryCount`，`ElapsedTime`并`RetryReason`哪些`long`、 一个`TimeSpan`和`Exception`分别。 第一次重新连接尝试前, 两者`PreviousRetryCount`和`ElapsedTime`将为零，和`RetryReason`将导致连接丢失的异常。 每个失败的重试尝试后,`PreviousRetryCount`将加 1，`ElapsedTime`随即更新以反映到目前为止，重新连接所用的时间量和`RetryReason`将导致最后一次的重新连接尝试失败的异常。

`NextRetryDelay` 必须返回任一一个 timespan，用于表示的时间等待下一次重新连接尝试或`null`如果`HubConnection`应停止重新连接。

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

或者，可以编写代码，如中所示手动将重新连接的客户端[手动重新连接](#manually-reconnect)。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在 3.0 之前，SignalR.NET 客户端不会自动重新连接。 必须编写代码将手动重新连接你的客户端。

::: moniker-end

使用<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件响应丢失连接。 例如，你可能想要自动执行重新连接。

`Closed`事件需要返回的委托`Task`，它允许异步代码运行，而不使用`async void`。 若要满足中的委托签名`Closed`运行以同步方式，返回的事件处理程序`Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

异步支持的主要原因是使您可以重新启动连接。 正在启动的连接是一个异步操作。

在`Closed`处理程序重新启动该连接，请考虑等待某些随机延迟，以防止服务器，重载，如下面的示例中所示：

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

`InvokeAsync` 在该中心将调用方法。 将中心方法名称和到中心方法中定义的任何参数传递`InvokeAsync`。 SignalR 是异步的因此请使用`async`和`await`时进行调用。

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync`方法将返回`Task`服务器方法返回时完成。 返回值，如果有的话，提供的结果作为`Task`。 在服务器上的方法引发的任何异常生成出错`Task`。 使用`await`要等待的时间才能完成的服务器方法的语法和`try...catch`语法来处理错误。

`SendAsync`方法将返回`Task`并完成时该消息已发送到服务器。 由于这未提供任何返回值`Task`不等待，直到服务器方法完成。 在发送消息时在客户端上引发的任何异常生成出错`Task`。 使用`await`和`try...catch`语法来处理发送错误。

> [!NOTE]
> 如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。 有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

## <a name="call-client-methods-from-hub"></a>从集线器调用客户端方法

定义使用集线器调用的方法`connection.On`之后生成，但之前启动连接。

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

在前面的代码`connection.On`运行时服务器端代码将调用它使用`SendAsync`方法。

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>错误处理和日志记录

处理 try catch 语句的错误。 检查`Exception`对象，以确定要执行后发生错误的适当操作。

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR 服务无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
