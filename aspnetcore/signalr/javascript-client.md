---
title: ASP.NET核心SignalRJavaScript客户端
author: bradygaster
description: ASP.NET核心SignalRJavaScript客户端概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994573"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET核心SignalRJavaScript客户端

作者：[Rachel Appel](https://twitter.com/rachelappel)

ASP.NET核心SignalRJavaScript 客户端库使开发人员能够调用服务器端集线器代码。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="install-the-opno-locsignalr-client-package"></a>安装SignalR客户端包

JavaScriptSignalR客户端库作为[npm](https://www.npmjs.com/)包提供。 以下各节概述了安装客户端库的不同方法。

### <a name="install-with-npm"></a>使用 npm 安装

如果使用 Visual Studio，则在根文件夹中运行**包管理器控制台**中的以下命令。 对于可视化工作室代码，请从**集成终端**运行以下命令。

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm 在*node_modules\\*文件夹中安装包内容。 在*wwwroot\\lib*文件夹下创建名为*信号器*的新文件夹。 将*Signalr.js*文件复制到*wwwroot_lib_signalr*文件夹。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm 在*node_modules\\*文件夹中安装包内容。 在*wwwroot\\lib*文件夹下创建名为*信号器*的新文件夹。 将*Signalr.js*文件复制到*wwwroot_lib_signalr*文件夹。

::: moniker-end

引用`<script>`元素SignalR中的 JavaScript 客户端。 例如：

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>使用内容交付网络 （CDN）

要使用没有 npm 先决条件的客户端库，请引用客户端库的 CDN 托管副本。 例如：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

客户端库在以下 CDN 上可用：

::: moniker range=">= aspnetcore-3.0"

* [恩杰斯](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [恩杰斯](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>使用 LibMan 安装

[LibMan](xref:client-side/libman/index)可用于从 CDN 托管的客户端库安装特定的客户端库文件。 例如，仅将已小的 JavaScript 文件添加到项目中。 有关该方法的详细信息，请参阅[SignalR添加客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。

## <a name="connect-to-a-hub"></a>连接到集线器

以下代码创建并启动连接。 中心的名称不区分大小写。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>交叉源连接

通常，浏览器将连接与请求的页面从同一域加载。 但是，有时需要连接到另一个域。

为了防止恶意站点从其他站点读取敏感数据，默认情况下将禁用[跨源连接](xref:security/cors)。 要允许跨源请求，请在类中`Startup`启用它。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>从客户端调用中心方法

JavaScript客户端通过[HubConnect](/javascript/api/%40aspnet/signalr/hubconnection)的[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。 该方法`invoke`接受两个参数：

* 中心方法的名称。 在下面的示例中，中心上的方法名称为`SendMessage`。
* 在中心方法中定义的任何参数。 在下面的示例中，参数名称为`message`。 示例代码使用除 Internet Explorer 以外的所有主要浏览器的当前版本中支持的箭头函数语法。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 如果在*无服务器模式下*使用SignalRAzure 服务，则无法从客户端调用集线器方法。 有关详细信息，请参阅[SignalR服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

该方法`invoke`返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。 当`Promise`服务器上的方法返回时，使用返回值（如果有）解析 。 如果服务器上的方法引发错误，`Promise`则 拒绝使用错误消息。 使用`then`本身`catch`上的`Promise`和 方法来处理这些情况（`await`或语法）。

该方法`send`返回JavaScript。 `Promise` 将`Promise`解解消息发送到服务器。 如果发送消息有错误，`Promise`则 拒绝使用错误消息。 使用`then`本身`catch`上的`Promise`和 方法来处理这些情况（`await`或语法）。

> [!NOTE]
> 使用`send`不会等到服务器收到消息。 因此，无法从服务器返回数据或错误。

## <a name="call-client-methods-from-hub"></a>从中心调用客户端方法

要从集线器接收消息，请使用 的[on](/javascript/api/%40aspnet/signalr/hubconnection#on)方法定义`HubConnection`方法。

* JavaScript 客户端方法的名称。 在下面的示例中，方法名称为`ReceiveMessage`。
* 中心传递给方法的参数。 在下面的示例中，参数值为`message`。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

当服务器端代码`connection.on`使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法调用它时，前面的代码将运行。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR通过匹配 和 中`SendAsync`定义的方法名称和参数来确定要调用的客户端方法`connection.on`。

> [!NOTE]
> 最佳做法是调用 后`HubConnection``on`上的[开始](/javascript/api/%40aspnet/signalr/hubconnection#start)方法。 这样做可确保在收到任何消息之前注册处理程序。

## <a name="error-handling-and-logging"></a>错误处理和日志记录

将`catch`方法链接到方法的末尾，`start`以处理客户端错误。 用于`console.error`将错误输出到浏览器的控制台。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

通过传递记录器和事件类型来在建立连接时记录客户端日志跟踪。 消息记录与指定的日志级别和更高。 可用日志级别如下：

* `signalR.LogLevel.Error`&ndash;错误消息。 仅`Error`记录邮件。
* `signalR.LogLevel.Warning`&ndash;有关潜在错误的警告消息。 日志`Warning`和`Error`消息。
* `signalR.LogLevel.Information`&ndash;状态消息，无错误。 日志`Information``Warning`和`Error`消息。
* `signalR.LogLevel.Trace`&ndash;跟踪消息。 记录所有内容，包括集线器和客户端之间传输的数据。

使用[HubConnectBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[配置日志记录](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。 消息将记录到浏览器控制台。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>重新连接客户端

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

JavaScriptSignalR客户端可以配置为使用`withAutomaticReconnect`[HubConnectBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自动重新连接。 默认情况下，它不会自动重新连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

没有任何参数，`withAutomaticReconnect()`将客户端配置为分别等待 0、2、10 和 30 秒，然后再尝试每次重新连接尝试，在四次尝试失败后停止。

在开始任何重新连接尝试之前，`HubConnection``HubConnectionState.Reconnecting`将转换为状态`onreconnecting`并触发其回调，而不是转换到`Disconnected`状态并触发其`onclose`回调，就像未配置自动重新连接一`HubConnection`样。 这提供了一个机会来警告用户连接已丢失并禁用 UI 元素。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

如果客户端在前四次尝试中成功重新连接，则 将`HubConnection`转换回`Connected`状态并触发其`onreconnected`回调。 这提供了一个机会，通知用户连接已重新建立。

由于连接对服务器看起来完全新，因此将为`connectionId``onreconnected`回调提供新的连接。

> [!WARNING]
> 如果`onreconnected`配置为[跳过协商](xref:signalr/configuration#configure-client-options) `connectionId` ，`HubConnection`则回调的参数将未定义。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`不会配置 以`HubConnection`重试初始启动失败，因此需要手动处理启动失败：

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

如果客户端在前四次尝试中未成功重新连接，则 将`HubConnection`转换为`Disconnected`状态并触发其[关闭](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。 这提供了一个机会，通知用户连接已永久丢失，并建议刷新页面：

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

为了在断开连接或更改重新连接计时之前配置自定义的重新连接尝试次数，`withAutomaticReconnect`请接受表示延迟（以毫秒为单位）的数字数组，以等待，然后再开始每次重新连接尝试。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前面的示例配置 要`HubConnection`在连接丢失后立即开始尝试重新连接。 对于默认配置也是如此。

如果第一次重新连接尝试失败，第二次重新连接尝试也将立即开始，而不是像默认配置中那样等待 2 秒。

如果第二次重新连接尝试失败，第三次重新连接尝试将在 10 秒内开始，这再次类似于默认配置。

然后，自定义行为会在第三次重新连接尝试失败后停止，而不是像在默认配置中那样在 30 秒内尝试再次重新连接尝试，从而再次偏离默认行为。

如果希望对自动重新连接尝试的计时和次数进行更多控制，请`withAutomaticReconnect`接受实现`IRetryPolicy`接口的对象，该对象具有名为 的`nextRetryDelayInMilliseconds`单个方法。

`nextRetryDelayInMilliseconds`使用 类型`RetryContext`获取单个参数。 有`RetryContext`三个属性： `previousRetryCount``elapsedMilliseconds`和`retryReason`分别为`number`a`number`和`Error`。 在第一次重新连接尝试之前，`previousRetryCount`两`elapsedMilliseconds`者都将为零，`retryReason`并且将是导致连接丢失的错误。 每次失败的重试尝试（`previousRetryCount`将递增为 1）后，`elapsedMilliseconds`将更新以反映到目前为止重新连接所花费的时间（以毫秒为单位），`retryReason`并且将是导致上次重新连接尝试失败的错误。

`nextRetryDelayInMilliseconds`必须返回表示在下一次重新连接尝试之前等待的毫秒数的数字，`null``HubConnection`或者是否应停止重新连接。

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

或者，您可以编写代码，这些代码将手动重新连接客户端，如[手动重新连接](#manually-reconnect)中所示。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在 3.0 之前，的SignalRJavaScript 客户端不会自动重新连接。 您必须编写代码，以手动重新连接客户端。

::: moniker-end

以下代码演示了典型的手动重新连接方法：

1. 创建函数（在本例中为`start`函数）以启动连接。
1. 在`start`连接`onclose`的事件处理程序中调用函数。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

实际实现将使用指数级回退或在放弃之前重试指定次数。

## <a name="additional-resources"></a>其他资源

* [JavaScript API 参考](/javascript/api/?view=signalr-js-latest)
* [JavaScript 教程](xref:tutorials/signalr)
* [WebPack 和 TypeScript 教程](xref:tutorials/signalr-typescript-webpack)
* [枢纽](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [跨源请求 （CORS）](xref:security/cors)
* [AzureSignalR服务无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
