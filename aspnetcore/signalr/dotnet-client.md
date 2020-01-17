---
title: .NET 客户端 SignalR ASP.NET Core
author: bradygaster
description: 有关 .NET 客户端 SignalR ASP.NET Core 的信息
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 39d9eccdb1e0457b177e75e6f94f3dd185b0093d
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146311"
---
# <a name="aspnet-core-opno-locsignalr-net-client"></a>.NET 客户端 SignalR ASP.NET Core

.NET 客户端库的 ASP.NET Core SignalR 使你可以从 .NET 应用程序与 SignalR 中心进行通信。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

本文中的代码示例是使用 .NET 客户端 SignalR ASP.NET Core 的 WPF 应用程序。

## <a name="install-the-opno-locsignalr-net-client-package"></a>安装 SignalR .NET 客户端包

[AspNetCore.SignalR。](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)要使 .net 客户端连接到 SignalR 集线器，需要客户端包。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

若要安装客户端库，请在 "**包管理器控制台**" 窗口中运行以下命令：

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

若要安装客户端库，请在命令 shell 中运行以下命令：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>连接到中心

若要建立连接，请创建 `HubConnectionBuilder` 并调用 `Build`。 在建立连接时，可以配置中心 URL、协议、传输类型、日志级别、标头和其他选项。 通过在 `Build`中插入任意 `HubConnectionBuilder` 方法来配置任何所需的选项。 开始与 `StartAsync`的连接。

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>处理丢失的连接

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

可以将 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> 配置为使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>上的 `WithAutomaticReconnect` 方法自动重新连接。 默认情况下，它不会自动重新连接。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

如果没有任何参数，`WithAutomaticReconnect()` 会将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。

在开始任何重新连接尝试之前，`HubConnection` 将转换为 `HubConnectionState.Reconnecting` 状态，并激发 `Reconnecting` 事件。  这为用户提供警告连接已丢失并禁用 UI 元素的机会。 非交互式应用可以开始排队或删除消息。

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

如果客户端在其前四次尝试内成功重新连接，则 `HubConnection` 将转换回 `Connected` 状态，并激发 `Reconnected` 事件。 这为用户提供了通知用户已重新建立连接并取消排队消息的排队的机会。

由于连接完全是服务器的新内容，因此向 `Reconnected` 事件处理程序提供了一个新的 `ConnectionId`。

> [!WARNING]
> 如果 `HubConnection` 配置为[跳过协商](xref:signalr/configuration#configure-client-options)，则 `Reconnected` 事件处理程序的 `connectionId` 参数将为 null。

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` 不会将 `HubConnection` 配置为重试初始启动失败，因此，需要手动处理启动失败：

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

如果客户端在其前四次尝试中未成功重新连接，则 `HubConnection` 将转换为 `Disconnected` 状态，并激发 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件。 这为尝试手动重新启动连接或通知用户连接永久丢失有机会。

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

为了在断开连接或更改重新连接时间安排之前配置自定义的重新连接尝试次数，`WithAutomaticReconnect` 接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

前面的示例将 `HubConnection` 配置为在连接丢失后立即开始尝试重新连接。 这也适用于默认配置。

如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。

如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。

然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离。 在默认配置中，将在另一个30秒内再次尝试重新连接。

如果需要更好地控制计时和自动重新连接尝试的次数，`WithAutomaticReconnect` 接受一个实现 `IRetryPolicy` 接口的对象，该对象具有名为 `NextRetryDelay`的单个方法。

`NextRetryDelay` 采用 `RetryContext`类型的单个自变量。 `RetryContext` 具有三个属性： `PreviousRetryCount`、`ElapsedTime` 和 `RetryReason`，分别为 `long`、`TimeSpan` 和 `Exception`。 第一次重新连接尝试之前，`PreviousRetryCount` 和 `ElapsedTime` 均为零，`RetryReason` 将是导致连接丢失的异常。 每次重试失败后，`PreviousRetryCount` 会递增1，`ElapsedTime` 将更新以反映到目前为止所用的时间量，`RetryReason` 将是导致上次重新连接尝试失败的异常。

`NextRetryDelay` 必须返回一个 TimeSpan，表示在下一次重新连接尝试之前等待的时间，或 `null` 如果 `HubConnection` 应停止重新连接。

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
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
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

或者，你可以编写将手动重新连接客户端的代码，如[手动重新连接](#manually-reconnect)中所示。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在3.0 之前，SignalR 的 .NET 客户端不会自动重新连接。 必须编写代码将手动重新连接你的客户端。

::: moniker-end

使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件来响应丢失的连接。 例如，你可能需要自动重新连接。

`Closed` 事件需要一个返回 `Task`的委托，该委托允许异步代码在不使用 `async void`的情况下运行。 若要在同步运行的 `Closed` 事件处理程序中满足委托签名，请返回 `Task.CompletedTask`：

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

异步支持的主要原因是，可以重启连接。 启动连接是一种异步操作。

在重启连接的 `Closed` 处理程序中，考虑等待一些随机延迟以防止服务器过载，如以下示例中所示：

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

`InvokeAsync` 在集线器上调用方法。 将 hub 方法名称和在 hub 方法中定义的所有参数传递到 `InvokeAsync`。 SignalR 是异步的，因此在进行调用时使用 `async` 和 `await`。

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync` 方法返回一个在服务器方法返回时完成的 `Task`。 返回值（如果有）作为 `Task`的结果提供。 服务器上的方法所引发的任何异常都会产生出错的 `Task`。 使用 `await` 语法等待服务器方法完成，并 `try...catch` 语法来处理错误。

`SendAsync` 方法返回一个在消息已发送到服务器时完成的 `Task`。 不会提供返回值，因为此 `Task` 不会等待服务器方法完成。 发送消息时，在客户端上引发的任何异常都会产生出错的 `Task`。 使用 `await` 和 `try...catch` 语法处理发送错误。

> [!NOTE]
> 如果在*无服务器模式下*使用 Azure SignalR 服务，则无法从客户端调用集线器方法。 有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

## <a name="call-client-methods-from-hub"></a>从集线器调用客户端方法

定义集线器在生成之后但在启动连接之前 `connection.On` 调用的方法。

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

当服务器端代码使用 `SendAsync` 方法调用时，`connection.On` 中的前面的代码将运行。

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>错误处理和日志记录

使用 try-catch 语句处理错误。 检查 `Exception` 对象，以确定在发生错误之后要执行的适当操作。

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR Service 无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
