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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="2b364-103">ASP.NET Core SignalR JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="2b364-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="2b364-104">作者：[Rachel Appel](http://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="2b364-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="2b364-105">ASP.NET Core SignalR JavaScript 客户端库，开发人员可以调用服务器端中心代码。</span><span class="sxs-lookup"><span data-stu-id="2b364-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="2b364-106">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2b364-106">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="2b364-107">安装 SignalR 客户端包</span><span class="sxs-lookup"><span data-stu-id="2b364-107">Install the SignalR client package</span></span>

<span data-ttu-id="2b364-108">作为提供 SignalR JavaScript 客户端库[npm](https://www.npmjs.com/)包。</span><span class="sxs-lookup"><span data-stu-id="2b364-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="2b364-109">如果使用 Visual Studio，运行`npm install`从**程序包管理器控制台**时的根文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2b364-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="2b364-110">对于 Visual Studio Code 中，运行中的命令**集成终端**。</span><span class="sxs-lookup"><span data-stu-id="2b364-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="2b364-111">npm 安装中的包内容 *node_modules\\@aspnet\signalr\dist\browser* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2b364-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="2b364-112">创建一个名为的新文件夹*signalr*下*wwwroot\\lib*文件夹。</span><span class="sxs-lookup"><span data-stu-id="2b364-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="2b364-113">复制 *signalr.js* 的文件 *wwwroot\lib\signalr* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2b364-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="2b364-114">使用 SignalR JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="2b364-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="2b364-115">引用中的 SignalR JavaScript 客户端`<script>`元素。</span><span class="sxs-lookup"><span data-stu-id="2b364-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="2b364-116">连接到中心</span><span class="sxs-lookup"><span data-stu-id="2b364-116">Connect to a hub</span></span>

<span data-ttu-id="2b364-117">下面的代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="2b364-118">在中心的名称是不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2b364-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="2b364-119">跨域的连接</span><span class="sxs-lookup"><span data-stu-id="2b364-119">Cross-origin connections</span></span>

<span data-ttu-id="2b364-120">通常情况下，浏览器请求的页面所在的域从加载的连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="2b364-121">但是，有一些情况下需要与另一个域的连接时。</span><span class="sxs-lookup"><span data-stu-id="2b364-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="2b364-122">若要防止恶意站点读取另一个站点中的敏感数据[跨域连接](xref:security/cors)默认处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="2b364-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="2b364-123">若要允许跨域请求，请启用它在`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="2b364-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="2b364-124">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="2b364-124">Call hub methods from client</span></span>

<span data-ttu-id="2b364-125">JavaScript 客户端上中心通过调用公共方法[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)。</span><span class="sxs-lookup"><span data-stu-id="2b364-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="2b364-126">`invoke`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="2b364-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="2b364-127">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="2b364-127">The name of the hub method.</span></span> <span data-ttu-id="2b364-128">在以下示例中，中心上的方法名称是`SendMessage`。</span><span class="sxs-lookup"><span data-stu-id="2b364-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="2b364-129">在集线器方法中定义的任何参数。</span><span class="sxs-lookup"><span data-stu-id="2b364-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="2b364-130">在以下示例中，参数名称是`message`。</span><span class="sxs-lookup"><span data-stu-id="2b364-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="2b364-131">示例代码使用的除 Internet Explorer 的所有主要浏览器的当前版本中支持的箭头函数语法。</span><span class="sxs-lookup"><span data-stu-id="2b364-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="2b364-132">如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="2b364-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="2b364-133">有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="2b364-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="2b364-134">`invoke`方法返回一个 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="2b364-134">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="2b364-135">`Promise`解析的返回值 （如果有） 在服务器上的方法返回时。</span><span class="sxs-lookup"><span data-stu-id="2b364-135">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="2b364-136">如果在服务器上的方法将引发错误，`Promise`拒绝并显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-136">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="2b364-137">使用`then`并`catch`上的方法`Promise`本身来处理这些情况下 (或`await`语法)。</span><span class="sxs-lookup"><span data-stu-id="2b364-137">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="2b364-138">`send`方法返回 JavaScript `Promise`。</span><span class="sxs-lookup"><span data-stu-id="2b364-138">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="2b364-139">`Promise`得到解决时，将消息发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="2b364-139">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="2b364-140">如果错误消息，发送`Promise`拒绝并显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-140">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="2b364-141">使用`then`并`catch`上的方法`Promise`本身来处理这些情况下 (或`await`语法)。</span><span class="sxs-lookup"><span data-stu-id="2b364-141">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="2b364-142">使用`send`不等待，直到服务器收到该消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-142">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="2b364-143">因此，不能从服务器返回的数据或错误。</span><span class="sxs-lookup"><span data-stu-id="2b364-143">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="2b364-144">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="2b364-144">Call client methods from hub</span></span>

<span data-ttu-id="2b364-145">若要从中心接收消息，定义方法使用[上](/javascript/api/%40aspnet/signalr/hubconnection#on)方法的`HubConnection`。</span><span class="sxs-lookup"><span data-stu-id="2b364-145">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="2b364-146">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="2b364-146">The name of the JavaScript client method.</span></span> <span data-ttu-id="2b364-147">在以下示例中，方法名称是`ReceiveMessage`。</span><span class="sxs-lookup"><span data-stu-id="2b364-147">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="2b364-148">该中心将传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="2b364-148">Arguments the hub passes to the method.</span></span> <span data-ttu-id="2b364-149">在以下示例中，参数值是`message`。</span><span class="sxs-lookup"><span data-stu-id="2b364-149">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="2b364-150">在前面的代码`connection.on`运行时服务器端代码将调用它使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法。</span><span class="sxs-lookup"><span data-stu-id="2b364-150">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="2b364-151">SignalR 确定要进行匹配的方法名称来调用的客户端方法和参数中定义`SendAsync`和`connection.on`。</span><span class="sxs-lookup"><span data-stu-id="2b364-151">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="2b364-152">最佳做法是，调用[启动](/javascript/api/%40aspnet/signalr/hubconnection#start)方法`HubConnection`后`on`。</span><span class="sxs-lookup"><span data-stu-id="2b364-152">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="2b364-153">这样做可确保您的处理程序注册之前接收任何消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-153">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="2b364-154">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="2b364-154">Error handling and logging</span></span>

<span data-ttu-id="2b364-155">链`catch`方法的末尾`start`方法以处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="2b364-155">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="2b364-156">使用`console.error`向浏览器的控制台输出错误。</span><span class="sxs-lookup"><span data-stu-id="2b364-156">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="2b364-157">通过传递要进行连接时记录的记录器和事件类型的安装程序客户端的日志跟踪。</span><span class="sxs-lookup"><span data-stu-id="2b364-157">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="2b364-158">使用指定的日志级别和更高版本记录的消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-158">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="2b364-159">可用日志级别为按如下所示：</span><span class="sxs-lookup"><span data-stu-id="2b364-159">Available log levels are as follows:</span></span>

* <span data-ttu-id="2b364-160">`signalR.LogLevel.Error` &ndash; 错误消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-160">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="2b364-161">日志`Error`仅消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-161">Logs `Error` messages only.</span></span>
* <span data-ttu-id="2b364-162">`signalR.LogLevel.Warning` &ndash; 有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-162">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="2b364-163">日志`Warning`，和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-163">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="2b364-164">`signalR.LogLevel.Information` &ndash; 不包含错误的状态消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-164">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="2b364-165">日志`Information`， `Warning`，和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-165">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="2b364-166">`signalR.LogLevel.Trace` &ndash; 跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="2b364-166">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="2b364-167">记录所有内容，包括数据中心和客户端之间传输。</span><span class="sxs-lookup"><span data-stu-id="2b364-167">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="2b364-168">使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)若要配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="2b364-168">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="2b364-169">消息会记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="2b364-169">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="2b364-170">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="2b364-170">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="2b364-171">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="2b364-171">Automatically reconnect</span></span>

<span data-ttu-id="2b364-172">可以将 SignalR JavaScript 客户端配置为自动重新连接，使用`withAutomaticReconnect`方法[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="2b364-172">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="2b364-173">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-173">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="2b364-174">不带任何参数，`withAutomaticReconnect()`配置客户端等待分别之前尝试每次重新连接尝试后四个失败的尝试, 停止 0、 2、 10 和 30 秒。</span><span class="sxs-lookup"><span data-stu-id="2b364-174">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="2b364-175">在开始任何重新连接尝试之前,`HubConnection`将转换到`HubConnectionState.Reconnecting`状态，并触发其`onreconnecting`回调而不是过渡到`Disconnected`状态和触发其`onclose`回调喜欢`HubConnection`没有自动重新连接配置。</span><span class="sxs-lookup"><span data-stu-id="2b364-175">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="2b364-176">这提供了机会来警告用户该连接已丢失，并禁用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="2b364-176">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="2b364-177">如果客户端成功地重新连接在其前四个尝试`HubConnection`将转换回`Connected`状态，并触发其`onreconnected`回调。</span><span class="sxs-lookup"><span data-stu-id="2b364-177">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="2b364-178">这提供机会通知，告知用户重新建立连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-178">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="2b364-179">由于连接到服务器，完全新查找新`connectionId`将提供给`onreconnected`回调。</span><span class="sxs-lookup"><span data-stu-id="2b364-179">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="2b364-180">`onreconnected`回调的`connectionId`参数将是未定义，如果`HubConnection`配置为[跳过协商](xref:signalr/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="2b364-180">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="2b364-181">`withAutomaticReconnect()` 不会配置`HubConnection`重试初始启动时失败，因此需要手动处理发生启动失败：</span><span class="sxs-lookup"><span data-stu-id="2b364-181">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="2b364-182">如果客户端不会成功地重新连接在其前四个尝试`HubConnection`将转换到`Disconnected`状态，并触发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。</span><span class="sxs-lookup"><span data-stu-id="2b364-182">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="2b364-183">这提供了机会通知，告知用户连接已永久丢失，建议刷新此页：</span><span class="sxs-lookup"><span data-stu-id="2b364-183">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

<span data-ttu-id="2b364-184">若要配置自定义多个断开连接之前重新连接尝试，或更改重新连接时间`withAutomaticReconnect`接受数字表示以毫秒为单位的延迟开始每次重新连接尝试前等待的数组。</span><span class="sxs-lookup"><span data-stu-id="2b364-184">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="2b364-185">前面的示例配置`HubConnection`启动会断开连接后立即尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-185">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="2b364-186">这也是默认配置，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="2b364-186">This is also true for the default configuration.</span></span>

<span data-ttu-id="2b364-187">如果第一次重新连接尝试失败，第二次重新连接尝试将还立即开始而不是等待 2 秒像默认配置。</span><span class="sxs-lookup"><span data-stu-id="2b364-187">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="2b364-188">如果第二次重新连接尝试失败，第三次重新连接尝试会在 10 秒内再次就像默认配置。</span><span class="sxs-lookup"><span data-stu-id="2b364-188">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="2b364-189">自定义行为然后偏离再次的默认行为通过停止第三个重新连接尝试而不是一个尝试失败后更重新连接尝试在像那样在默认配置中的另一个 30 秒内。</span><span class="sxs-lookup"><span data-stu-id="2b364-189">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="2b364-190">如果你想甚至更好地控制时间安排和数量自动重新连接尝试`withAutomaticReconnect`接受一个对象，实现`IReconnectPolicy`接口，它具有一个名为的单个方法`nextRetryDelayInMilliseconds`。</span><span class="sxs-lookup"><span data-stu-id="2b364-190">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="2b364-191">`nextRetryDelayInMilliseconds` 采用两个参数`previousRetryCount`和`elapsedMilliseconds`，这是这两个数字。</span><span class="sxs-lookup"><span data-stu-id="2b364-191">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="2b364-192">第一次重新连接尝试前, 两者`previousRetryCount`和`elapsedMilliseconds`将为零。</span><span class="sxs-lookup"><span data-stu-id="2b364-192">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="2b364-193">每个失败的重试尝试后,`previousRetryCount`将递增 1 并`elapsedMilliseconds`将更新以反映正在重新连接以毫秒为单位的到目前为止所用的时间量。</span><span class="sxs-lookup"><span data-stu-id="2b364-193">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="2b364-194">`nextRetryDelayInMilliseconds` 必须返回数字表示的毫秒数的下一次重新连接尝试之前要等待或`null`如果`HubConnection`应停止重新连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-194">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="2b364-195">或者，可以编写代码，如中所示手动将重新连接的客户端[手动重新连接](#manually-reconnect)。</span><span class="sxs-lookup"><span data-stu-id="2b364-195">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="2b364-196">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="2b364-196">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="2b364-197">在 3.0 之前，SignalR JavaScript 客户端不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-197">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="2b364-198">必须编写代码将手动重新连接你的客户端。</span><span class="sxs-lookup"><span data-stu-id="2b364-198">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="2b364-199">下面的代码演示了典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="2b364-199">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="2b364-200">一个函数 (在这种情况下，`start`函数) 创建以启动连接。</span><span class="sxs-lookup"><span data-stu-id="2b364-200">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="2b364-201">调用`start`中的连接函数`onclose`事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="2b364-201">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="2b364-202">实际的实现会使用指数退让或重试的次数后放弃了指定的次数。</span><span class="sxs-lookup"><span data-stu-id="2b364-202">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="2b364-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="2b364-203">Additional resources</span></span>

* [<span data-ttu-id="2b364-204">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="2b364-204">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="2b364-205">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="2b364-205">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2b364-206">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="2b364-206">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="2b364-207">中心</span><span class="sxs-lookup"><span data-stu-id="2b364-207">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2b364-208">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="2b364-208">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2b364-209">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="2b364-209">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="2b364-210">跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="2b364-210">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="2b364-211">Azure SignalR 服务无服务器文档</span><span class="sxs-lookup"><span data-stu-id="2b364-211">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
