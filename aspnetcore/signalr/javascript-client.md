---
title: ASP.NET Core SignalR JavaScript 客户端
author: bradygaster
description: ASP.NET Core SignalR JavaScript 客户端概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 3086b4aa532dfe992e19c193ef76f216f7835164
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651534"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET Core SignalR JavaScript 客户端

作者：[Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript 客户端库使开发人员能够调用服务器端集线器代码。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="install-the-opno-locsignalr-client-package"></a>安装 SignalR 客户端包

SignalR JavaScript 客户端库作为[npm](https://www.npmjs.com/)包提供。 如果使用的是 Visual Studio，请在根文件夹中的 "**包管理器控制台**" 中运行 `npm install`。 对于 Visual Studio Code，请从**集成终端**运行命令。

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

npm 将包内容安装*node_modules\\@microsoft\signalr\dist\browser* 文件夹中。 在*wwwroot\\lib*文件夹下，创建名为*signalr*的新文件夹。 将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm 将包内容安装*node_modules\\@aspnet\signalr\dist\browser* 文件夹中。 在*wwwroot\\lib*文件夹下，创建名为*signalr*的新文件夹。 将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>使用 SignalR JavaScript 客户端

引用 `<script>` 元素中的 SignalR JavaScript 客户端。

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>连接到中心

下面的代码创建并启动连接。 在中心的名称是不区分大小写。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>跨域的连接

通常情况下，浏览器请求的页面所在的域从加载的连接。 但是，有一些情况下需要与另一个域的连接时。

为了防止恶意站点读取其他站点中的敏感数据，默认情况下会禁用[跨域连接](xref:security/cors)。 若要允许跨源请求，请在 `Startup` 类中启用它。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

JavaScript 客户端通过[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。 `invoke` 方法接受两个参数：

* 集线器方法的名称。 在下面的示例中，集线器上的方法名称是 `SendMessage`。
* 在集线器方法中定义的任何参数。 在下面的示例中，参数名称为 `message`。 示例代码使用了在所有主要浏览器（Internet Explorer 除外）的当前版本中受支持的箭头函数语法。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 如果在*无服务器模式下*使用 Azure SignalR 服务，则无法从客户端调用集线器方法。 有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

`invoke` 方法返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。 当服务器上的方法返回时，将用返回值（如果有）解析 `Promise`。 如果服务器上的方法引发错误，则会拒绝 `Promise` 错误消息。 使用 `Promise` 本身的 `then` 和 `catch` 方法来处理这些情况（或 `await` 语法）。

`send` 方法返回 JavaScript `Promise`。 当消息发送到服务器时，将解析 `Promise`。 如果发送消息时出错，则会拒绝 `Promise`，并会出现错误消息。 使用 `Promise` 本身的 `then` 和 `catch` 方法来处理这些情况（或 `await` 语法）。

> [!NOTE]
> 使用 `send` 不会等到服务器收到消息。 因此，不可能从服务器返回数据或错误。

## <a name="call-client-methods-from-hub"></a>从集线器调用客户端方法

若要从中心接收消息，请使用 `HubConnection`的[on](/javascript/api/%40aspnet/signalr/hubconnection#on)方法定义方法。

* JavaScript 客户端方法的名称。 在下面的示例中，方法名称是 `ReceiveMessage`。
* 该中心将传递给方法的参数。 在下面的示例中，参数值是 `message`。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

当服务器端代码使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法调用时，`connection.on` 中的前面的代码将运行。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR 通过匹配 `SendAsync` 和 `connection.on`中定义的方法名称和参数来确定要调用的客户端方法。

> [!NOTE]
> 最佳做法是在 `on`后对 `HubConnection` 调用[start](/javascript/api/%40aspnet/signalr/hubconnection#start)方法。 这样做可确保您的处理程序注册之前接收任何消息。

## <a name="error-handling-and-logging"></a>错误处理和日志记录

将 `catch` 方法链接到 `start` 方法的末尾，以处理客户端错误。 使用 `console.error` 将错误输出到浏览器的控制台。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

通过传递要进行连接时记录的记录器和事件类型的安装程序客户端的日志跟踪。 使用指定的日志级别和更高版本记录的消息。 可用日志级别为按如下所示：

* `signalR.LogLevel.Error` &ndash; 错误消息。 仅记录 `Error` 消息。
* `signalR.LogLevel.Warning` &ndash; 有关潜在错误的警告消息。 记录 `Warning`和 `Error` 消息。
* `signalR.LogLevel.Information` &ndash; 状态消息而不发生错误。 记录 `Information`、`Warning`和 `Error` 消息。
* `signalR.LogLevel.Trace` &ndash; 跟踪消息。 记录所有内容，包括数据中心和客户端之间传输。

使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。 消息会记录到浏览器控制台。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>重新连接客户端

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

可以将 SignalR 的 JavaScript 客户端配置为使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的 `withAutomaticReconnect` 方法自动重新连接。 默认情况下，它不会自动重新连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

如果没有任何参数，`withAutomaticReconnect()` 会将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。

在开始任何重新连接尝试之前，`HubConnection` 将转换为 `HubConnectionState.Reconnecting` 状态，并激发其 `onreconnecting` 回调，而不是转换到 `Disconnected` 状态并触发其 `onclose` 回调，如未配置自动重新连接的 `HubConnection`。 这为用户提供警告连接已丢失并禁用 UI 元素的机会。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

如果客户端在其前四次尝试内成功重新连接，则 `HubConnection` 将转换回 `Connected` 状态，并激发其 `onreconnected` 回调。 这为用户提供了通知用户连接已重新建立的机会。

由于连接完全是服务器的新内容，因此向 `onreconnected` 回调提供了一个新的 `connectionId`。

> [!WARNING]
> 如果 `HubConnection` 配置为[跳过协商](xref:signalr/configuration#configure-client-options)，则不会定义 `onreconnected` 回调的 `connectionId` 参数。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` 不会将 `HubConnection` 配置为重试初始启动失败，因此，需要手动处理启动失败：

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

如果客户端在其前四次尝试中未成功重新连接，则 `HubConnection` 将转换为 `Disconnected` 状态，并激发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。 这为用户提供了通知用户连接永久丢失的机会，并建议刷新页面：

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

为了在断开连接或更改重新连接时间安排之前配置自定义的重新连接尝试次数，`withAutomaticReconnect` 接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前面的示例将 `HubConnection` 配置为在连接丢失后立即开始尝试重新连接。 这也适用于默认配置。

如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。

如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。

然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离，而不是在另一个30秒内尝试再次尝试重新连接，就像在默认配置中一样。

如果需要更好地控制计时和自动重新连接尝试的次数，`withAutomaticReconnect` 接受一个实现 `IRetryPolicy` 接口的对象，该对象具有名为 `nextRetryDelayInMilliseconds`的单个方法。

`nextRetryDelayInMilliseconds` 采用 `RetryContext`类型的单个自变量。 `RetryContext` 具有三个属性： `previousRetryCount`、`elapsedMilliseconds` 和 `retryReason` 分别为 `number`、`number` 和 `Error`。 第一次重新连接尝试之前，`previousRetryCount` 和 `elapsedMilliseconds` 均为零，`retryReason` 将是导致连接丢失的错误。 每次重试失败后，`previousRetryCount` 会递增1，`elapsedMilliseconds` 将更新以反映到目前为止的重新连接时间（以毫秒为单位），`retryReason` 将是导致上次重新连接尝试失败的错误。

`nextRetryDelayInMilliseconds` 必须返回一个数字，该数字表示在下一次重新连接尝试之前要等待的毫秒数，或者，如果 `HubConnection` 应停止重新连接，则为 `null`。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

或者，你可以编写将手动重新连接客户端的代码，如[手动重新连接](#manually-reconnect)中所示。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在3.0 之前，SignalR 的 JavaScript 客户端不会自动重新连接。 必须编写代码将手动重新连接你的客户端。

::: moniker-end

下面的代码演示典型的手动重新连接方法：

1. 创建函数（在本例中为 `start` 函数）以启动连接。
1. 在连接的 `onclose` 事件处理程序中调用 `start` 函数。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

实际的实现会使用指数退让或重试的次数后放弃了指定的次数。

## <a name="additional-resources"></a>其他资源

* [JavaScript API 参考](/javascript/api/?view=signalr-js-latest)
* [JavaScript 教程](xref:tutorials/signalr)
* [WebPack 和 TypeScript 教程](xref:tutorials/signalr-typescript-webpack)
* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [跨域请求（CORS）](xref:security/cors)
* [Azure SignalR Service 无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
