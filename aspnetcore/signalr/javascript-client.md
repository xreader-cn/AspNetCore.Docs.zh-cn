---
title: ASP.NET Core SignalR JavaScript 客户端
author: bradygaster
description: ASP.NET Core SignalR JavaScript 客户端的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: f1f072e63928502fa1bad62e808ff035e57f2fd3
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983008"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript 客户端

作者：[Rachel Appel](http://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript 客户端库，开发人员可以调用服务器端中心代码。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="install-the-signalr-client-package"></a>安装 SignalR 客户端包

作为提供 SignalR JavaScript 客户端库[npm](https://www.npmjs.com/)包。 如果使用 Visual Studio，运行`npm install`从**程序包管理器控制台**时的根文件夹中。 对于 Visual Studio Code 中，运行中的命令**集成终端**。

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm 安装中的包内容 *node_modules\\@aspnet\signalr\dist\browser* 文件夹。 创建一个名为的新文件夹*signalr*下*wwwroot\\lib*文件夹。 复制 *signalr.js* 的文件 *wwwroot\lib\signalr* 文件夹。

## <a name="use-the-signalr-javascript-client"></a>使用 SignalR JavaScript 客户端

引用中的 SignalR JavaScript 客户端`<script>`元素。

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>连接到中心

下面的代码创建并启动连接。 在中心的名称是不区分大小写。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>跨域的连接

通常情况下，浏览器请求的页面所在的域从加载的连接。 但是，有一些情况下需要与另一个域的连接时。

若要防止恶意站点读取另一个站点中的敏感数据[跨域连接](xref:security/cors)默认处于禁用状态。 若要允许跨域请求，请启用它在`Startup`类。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

JavaScript 客户端上中心通过调用公共方法[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)。 `invoke`方法接受两个参数：

* 集线器方法的名称。 在以下示例中，中心上的方法名称是`SendMessage`。
* 在集线器方法中定义的任何参数。 在以下示例中，参数名称是`message`。 示例代码使用的除 Internet Explorer 的所有主要浏览器的当前版本中支持的箭头函数语法。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。 有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

`invoke`方法返回一个 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。 `Promise`解析的返回值 （如果有） 在服务器上的方法返回时。 如果在服务器上的方法将引发错误，`Promise`拒绝并显示错误消息。 使用`then`并`catch`上的方法`Promise`本身来处理这些情况下 (或`await`语法)。

`send`方法返回 JavaScript `Promise`。 `Promise`得到解决时，将消息发送到服务器。 如果错误消息，发送`Promise`拒绝并显示错误消息。 使用`then`并`catch`上的方法`Promise`本身来处理这些情况下 (或`await`语法)。

> [!NOTE]
> 使用`send`不等待，直到服务器收到该消息。 因此，不能从服务器返回的数据或错误。

## <a name="call-client-methods-from-hub"></a>从集线器调用客户端方法

若要从中心接收消息，定义方法使用[上](/javascript/api/%40aspnet/signalr/hubconnection#on)方法的`HubConnection`。

* JavaScript 客户端方法的名称。 在以下示例中，方法名称是`ReceiveMessage`。
* 该中心将传递给方法的参数。 在以下示例中，参数值是`message`。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

在前面的代码`connection.on`运行时服务器端代码将调用它使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR 确定要进行匹配的方法名称来调用的客户端方法和参数中定义`SendAsync`和`connection.on`。

> [!NOTE]
> 最佳做法是，调用[启动](/javascript/api/%40aspnet/signalr/hubconnection#start)方法`HubConnection`后`on`。 这样做可确保您的处理程序注册之前接收任何消息。

## <a name="error-handling-and-logging"></a>错误处理和日志记录

链`catch`方法的末尾`start`方法以处理客户端错误。 使用`console.error`向浏览器的控制台输出错误。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

通过传递要进行连接时记录的记录器和事件类型的安装程序客户端的日志跟踪。 使用指定的日志级别和更高版本记录的消息。 可用日志级别为按如下所示：

* `signalR.LogLevel.Error` &ndash; 错误消息。 日志`Error`仅消息。
* `signalR.LogLevel.Warning` &ndash; 有关潜在错误的警告消息。 日志`Warning`，和`Error`消息。
* `signalR.LogLevel.Information` &ndash; 不包含错误的状态消息。 日志`Information`， `Warning`，和`Error`消息。
* `signalR.LogLevel.Trace` &ndash; 跟踪消息。 记录所有内容，包括数据中心和客户端之间传输。

使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)若要配置日志级别。 消息会记录到浏览器控制台。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>重新连接客户端

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

可以将 SignalR JavaScript 客户端配置为自动重新连接，使用`withAutomaticReconnect`方法[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)。 默认情况下，它不会自动重新连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

不带任何参数，`withAutomaticReconnect()`配置客户端等待分别之前尝试每次重新连接尝试后四个失败的尝试, 停止 0、 2、 10 和 30 秒。

在开始任何重新连接尝试之前,`HubConnection`将转换到`HubConnectionState.Reconnecting`状态，并触发其`onreconnecting`回调而不是过渡到`Disconnected`状态和触发其`onclose`回调喜欢`HubConnection`没有自动重新连接配置。 这提供了机会来警告用户该连接已丢失，并禁用 UI 元素。

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

如果客户端成功地重新连接在其前四个尝试`HubConnection`将转换回`Connected`状态，并触发其`onreconnected`回调。 这提供机会通知，告知用户重新建立连接。

由于连接到服务器，完全新查找新`connectionId`将提供给`onreconnected`回调。

> [!WARNING]
> `onreconnected`回调的`connectionId`参数将是未定义，如果`HubConnection`配置为[跳过协商](xref:signalr/configuration#configure-client-options)。

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` 不会配置`HubConnection`重试初始启动时失败，因此需要手动处理发生启动失败：

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

如果客户端不会成功地重新连接在其前四个尝试`HubConnection`将转换到`Disconnected`状态，并触发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。 这提供了机会通知，告知用户连接已永久丢失，建议刷新此页：

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

若要配置自定义多个断开连接之前重新连接尝试，或更改重新连接时间`withAutomaticReconnect`接受数字表示以毫秒为单位的延迟开始每次重新连接尝试前等待的数组。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前面的示例配置`HubConnection`启动会断开连接后立即尝试重新连接。 这也是默认配置，则返回 true。

如果第一次重新连接尝试失败，第二次重新连接尝试将还立即开始而不是等待 2 秒像默认配置。

如果第二次重新连接尝试失败，第三次重新连接尝试会在 10 秒内再次就像默认配置。

自定义行为然后偏离再次的默认行为通过停止第三个重新连接尝试而不是一个尝试失败后更重新连接尝试在像那样在默认配置中的另一个 30 秒内。

如果你想甚至更好地控制时间安排和数量自动重新连接尝试`withAutomaticReconnect`接受一个对象，实现`IReconnectPolicy`接口，它具有一个名为的单个方法`nextRetryDelayInMilliseconds`。

`nextRetryDelayInMilliseconds` 采用两个参数`previousRetryCount`和`elapsedMilliseconds`，这是这两个数字。 第一次重新连接尝试前, 两者`previousRetryCount`和`elapsedMilliseconds`将为零。 每个失败的重试尝试后,`previousRetryCount`将递增 1 并`elapsedMilliseconds`将更新以反映正在重新连接以毫秒为单位的到目前为止所用的时间量。

`nextRetryDelayInMilliseconds` 必须返回数字表示的毫秒数的下一次重新连接尝试之前要等待或`null`如果`HubConnection`应停止重新连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
          if (elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

或者，可以编写代码，如中所示手动将重新连接的客户端[手动重新连接](#manually-reconnect)。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在 3.0 之前，SignalR JavaScript 客户端不会自动重新连接。 必须编写代码将手动重新连接你的客户端。

::: moniker-end

下面的代码演示了典型的手动重新连接方法：

1. 一个函数 (在这种情况下，`start`函数) 创建以启动连接。
1. 调用`start`中的连接函数`onclose`事件处理程序。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

实际的实现会使用指数退让或重试的次数后放弃了指定的次数。 

## <a name="additional-resources"></a>其他资源

* [JavaScript API 参考](/javascript/api/?view=signalr-js-latest)
* [JavaScript 教程](xref:tutorials/signalr)
* [WebPack 和 TypeScript 教程](xref:tutorials/signalr-typescript-webpack)
* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [跨域请求 (CORS)](xref:security/cors)
* [Azure SignalR 服务无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
