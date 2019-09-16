---
title: ASP.NET Core SignalR JavaScript 客户端
author: bradygaster
description: ASP.NET Core SignalR JavaScript 客户端的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412379"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="56097-103">ASP.NET Core SignalR JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="56097-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="56097-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="56097-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="56097-105">ASP.NET Core SignalR JavaScript 客户端库，开发人员可以调用服务器端中心代码。</span><span class="sxs-lookup"><span data-stu-id="56097-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="56097-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="56097-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="56097-107">安装 SignalR 客户端包</span><span class="sxs-lookup"><span data-stu-id="56097-107">Install the SignalR client package</span></span>

<span data-ttu-id="56097-108">作为提供 SignalR JavaScript 客户端库[npm](https://www.npmjs.com/)包。</span><span class="sxs-lookup"><span data-stu-id="56097-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="56097-109">如果使用 Visual Studio，运行`npm install`从**程序包管理器控制台**时的根文件夹中。</span><span class="sxs-lookup"><span data-stu-id="56097-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="56097-110">对于 Visual Studio Code 中，运行中的命令**集成终端**。</span><span class="sxs-lookup"><span data-stu-id="56097-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="56097-111">npm 安装中的包内容 *node_modules\\@microsoft\signalr\dist\browser* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="56097-112">创建一个名为的新文件夹*signalr*下*wwwroot\\lib*文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="56097-113">复制 *signalr.js* 的文件 *wwwroot\lib\signalr* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="56097-114">npm 安装中的包内容 *node_modules\\@aspnet\signalr\dist\browser* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="56097-115">创建一个名为的新文件夹*signalr*下*wwwroot\\lib*文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="56097-116">复制 *signalr.js* 的文件 *wwwroot\lib\signalr* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="56097-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="56097-117">使用 SignalR JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="56097-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="56097-118">引用中的 SignalR JavaScript 客户端`<script>`元素。</span><span class="sxs-lookup"><span data-stu-id="56097-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="56097-119">连接到中心</span><span class="sxs-lookup"><span data-stu-id="56097-119">Connect to a hub</span></span>

<span data-ttu-id="56097-120">下面的代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="56097-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="56097-121">在中心的名称是不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="56097-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="56097-122">跨域的连接</span><span class="sxs-lookup"><span data-stu-id="56097-122">Cross-origin connections</span></span>

<span data-ttu-id="56097-123">通常情况下，浏览器请求的页面所在的域从加载的连接。</span><span class="sxs-lookup"><span data-stu-id="56097-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="56097-124">但是，有一些情况下需要与另一个域的连接时。</span><span class="sxs-lookup"><span data-stu-id="56097-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="56097-125">若要防止恶意站点读取另一个站点中的敏感数据[跨域连接](xref:security/cors)默认处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="56097-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="56097-126">若要允许跨域请求，请启用它在`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="56097-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="56097-127">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="56097-127">Call hub methods from client</span></span>

<span data-ttu-id="56097-128">JavaScript 客户端上中心通过调用公共方法[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)。</span><span class="sxs-lookup"><span data-stu-id="56097-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="56097-129">`invoke`方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="56097-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="56097-130">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="56097-130">The name of the hub method.</span></span> <span data-ttu-id="56097-131">在以下示例中，中心上的方法名称是`SendMessage`。</span><span class="sxs-lookup"><span data-stu-id="56097-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="56097-132">在集线器方法中定义的任何参数。</span><span class="sxs-lookup"><span data-stu-id="56097-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="56097-133">在以下示例中，参数名称是`message`。</span><span class="sxs-lookup"><span data-stu-id="56097-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="56097-134">示例代码使用了在所有主要浏览器（Internet Explorer 除外）的当前版本中受支持的箭头函数语法。</span><span class="sxs-lookup"><span data-stu-id="56097-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="56097-135">如果在*无服务器模式下*使用 Azure SignalR 服务，则无法从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="56097-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="56097-136">有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="56097-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="56097-137">`invoke`方法返回 JavaScript [承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="56097-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="56097-138">当服务器上的方法返回时，将解析为返回值（如果有）。`Promise`</span><span class="sxs-lookup"><span data-stu-id="56097-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="56097-139">如果服务器上的方法引发错误， `Promise`将拒绝，并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="56097-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="56097-140">`then`使用和`catch`方法`await`来处理这些情况（或语法）。 `Promise`</span><span class="sxs-lookup"><span data-stu-id="56097-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="56097-141">方法返回 JavaScript `Promise`。 `send`</span><span class="sxs-lookup"><span data-stu-id="56097-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="56097-142">当消息已发送到服务器时，将解决。`Promise`</span><span class="sxs-lookup"><span data-stu-id="56097-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="56097-143">如果发送消息时出错， `Promise`将拒绝，并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="56097-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="56097-144">`then`使用和`catch`方法`await`来处理这些情况（或语法）。 `Promise`</span><span class="sxs-lookup"><span data-stu-id="56097-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="56097-145">使用`send`不会等到服务器收到消息。</span><span class="sxs-lookup"><span data-stu-id="56097-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="56097-146">因此，不可能从服务器返回数据或错误。</span><span class="sxs-lookup"><span data-stu-id="56097-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="56097-147">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="56097-147">Call client methods from hub</span></span>

<span data-ttu-id="56097-148">若要从中心接收消息，定义方法使用[上](/javascript/api/%40aspnet/signalr/hubconnection#on)方法的`HubConnection`。</span><span class="sxs-lookup"><span data-stu-id="56097-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="56097-149">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="56097-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="56097-150">在以下示例中，方法名称是`ReceiveMessage`。</span><span class="sxs-lookup"><span data-stu-id="56097-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="56097-151">该中心将传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="56097-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="56097-152">在以下示例中，参数值是`message`。</span><span class="sxs-lookup"><span data-stu-id="56097-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="56097-153">在前面的代码`connection.on`运行时服务器端代码将调用它使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法。</span><span class="sxs-lookup"><span data-stu-id="56097-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="56097-154">SignalR 确定要进行匹配的方法名称来调用的客户端方法和参数中定义`SendAsync`和`connection.on`。</span><span class="sxs-lookup"><span data-stu-id="56097-154">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="56097-155">最佳做法是，调用[启动](/javascript/api/%40aspnet/signalr/hubconnection#start)方法`HubConnection`后`on`。</span><span class="sxs-lookup"><span data-stu-id="56097-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="56097-156">这样做可确保您的处理程序注册之前接收任何消息。</span><span class="sxs-lookup"><span data-stu-id="56097-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="56097-157">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="56097-157">Error handling and logging</span></span>

<span data-ttu-id="56097-158">链`catch`方法的末尾`start`方法以处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="56097-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="56097-159">使用`console.error`向浏览器的控制台输出错误。</span><span class="sxs-lookup"><span data-stu-id="56097-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="56097-160">通过传递要进行连接时记录的记录器和事件类型的安装程序客户端的日志跟踪。</span><span class="sxs-lookup"><span data-stu-id="56097-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="56097-161">使用指定的日志级别和更高版本记录的消息。</span><span class="sxs-lookup"><span data-stu-id="56097-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="56097-162">可用日志级别为按如下所示：</span><span class="sxs-lookup"><span data-stu-id="56097-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="56097-163">`signalR.LogLevel.Error` &ndash; 错误消息。</span><span class="sxs-lookup"><span data-stu-id="56097-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="56097-164">日志`Error`仅消息。</span><span class="sxs-lookup"><span data-stu-id="56097-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="56097-165">`signalR.LogLevel.Warning` &ndash; 有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="56097-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="56097-166">日志`Warning`，和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="56097-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="56097-167">`signalR.LogLevel.Information` &ndash; 不包含错误的状态消息。</span><span class="sxs-lookup"><span data-stu-id="56097-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="56097-168">日志`Information`， `Warning`，和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="56097-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="56097-169">`signalR.LogLevel.Trace` &ndash; 跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="56097-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="56097-170">记录所有内容，包括数据中心和客户端之间传输。</span><span class="sxs-lookup"><span data-stu-id="56097-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="56097-171">使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)若要配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="56097-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="56097-172">消息会记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="56097-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="56097-173">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="56097-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="56097-174">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="56097-174">Automatically reconnect</span></span>

<span data-ttu-id="56097-175">可以将 SignalR 的 JavaScript 客户端配置为使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的`withAutomaticReconnect`方法自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56097-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="56097-176">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56097-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="56097-177">在没有任何参数`withAutomaticReconnect()`的情况下，会将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。</span><span class="sxs-lookup"><span data-stu-id="56097-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="56097-178">在开始任何重新连接尝试之前`HubConnection` ，将转换`HubConnectionState.Reconnecting`为状态并激发其`onreconnecting`回调，而不是转换`Disconnected`为状态，并`onclose`触发其回调，如`HubConnection`未配置自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56097-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="56097-179">这为用户提供警告连接已丢失并禁用 UI 元素的机会。</span><span class="sxs-lookup"><span data-stu-id="56097-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56097-180">如果客户端在其前四次尝试内成功重新`HubConnection`连接，则将转换`Connected`回状态并激发`onreconnected`其回调。</span><span class="sxs-lookup"><span data-stu-id="56097-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="56097-181">这为用户提供了通知用户连接已重新建立的机会。</span><span class="sxs-lookup"><span data-stu-id="56097-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="56097-182">由于连接在服务器上看起来是全新的，因此`connectionId`将`onreconnected`向回调提供一个新的。</span><span class="sxs-lookup"><span data-stu-id="56097-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="56097-183">如果 `HubConnection`配置为[跳过协商](xref:signalr/configuration#configure-client-options)，则`onreconnected`不会定义回调的`connectionId`参数。</span><span class="sxs-lookup"><span data-stu-id="56097-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56097-184">`withAutomaticReconnect()`不会将`HubConnection`配置为重试初始启动失败，因此，需要手动处理启动失败：</span><span class="sxs-lookup"><span data-stu-id="56097-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="56097-185">如果客户端在其前四次尝试中未成功重新`HubConnection`连接，则将`Disconnected`转换为状态并激发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。</span><span class="sxs-lookup"><span data-stu-id="56097-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="56097-186">这为用户提供了通知用户连接永久丢失的机会，并建议刷新页面：</span><span class="sxs-lookup"><span data-stu-id="56097-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56097-187">若要在断开连接或更改重新连接时间安排之前配置自定义的重新连接`withAutomaticReconnect`尝试次数，请接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="56097-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="56097-188">前面的示例将配置`HubConnection`为在连接丢失后立即开始尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="56097-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="56097-189">这也适用于默认配置。</span><span class="sxs-lookup"><span data-stu-id="56097-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="56097-190">如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="56097-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="56097-191">如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。</span><span class="sxs-lookup"><span data-stu-id="56097-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="56097-192">然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离，而不是在另一个30秒内尝试再次尝试重新连接，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="56097-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="56097-193">如果需要更好地控制计时和自动重新连接尝试的次数， `withAutomaticReconnect`则接受一个`IRetryPolicy`实现接口的对象，该对象具有一个名为`nextRetryDelayInMilliseconds`的方法。</span><span class="sxs-lookup"><span data-stu-id="56097-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="56097-194">`nextRetryDelayInMilliseconds`采用类型`RetryContext`为的单个自变量。</span><span class="sxs-lookup"><span data-stu-id="56097-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="56097-195">`previousRetryCount`具有三`elapsedMilliseconds`个属性：和`retryReason`分别为`number` 、和。 `number` `Error` `RetryContext`</span><span class="sxs-lookup"><span data-stu-id="56097-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="56097-196">第一次重新连接尝试之前`previousRetryCount` ， `elapsedMilliseconds`和都`retryReason`是零，将是导致连接丢失的错误。</span><span class="sxs-lookup"><span data-stu-id="56097-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="56097-197">每次失败的重试`previousRetryCount`次数递增一次后， `elapsedMilliseconds`将进行更新，以反映到目前为止的重新连接所用的时间（以`retryReason`毫秒为单位），将是导致上次重新连接尝试的错误失败.</span><span class="sxs-lookup"><span data-stu-id="56097-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="56097-198">`nextRetryDelayInMilliseconds`必须返回一个数字，该数字表示在下一次重新连接尝试之前要等待`null`的毫秒`HubConnection`数，或者，如果应停止重新连接，则为。</span><span class="sxs-lookup"><span data-stu-id="56097-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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
        })
    .build();
```

<span data-ttu-id="56097-199">或者，你可以编写将手动重新连接客户端的代码，如[手动重新连接](#manually-reconnect)中所示。</span><span class="sxs-lookup"><span data-stu-id="56097-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="56097-200">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="56097-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="56097-201">在3.0 之前，SignalR 的 JavaScript 客户端不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56097-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="56097-202">必须编写代码将手动重新连接你的客户端。</span><span class="sxs-lookup"><span data-stu-id="56097-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="56097-203">下面的代码演示典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="56097-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="56097-204">一个函数 (在这种情况下，`start`函数) 创建以启动连接。</span><span class="sxs-lookup"><span data-stu-id="56097-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="56097-205">调用`start`中的连接函数`onclose`事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="56097-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="56097-206">实际的实现会使用指数退让或重试的次数后放弃了指定的次数。</span><span class="sxs-lookup"><span data-stu-id="56097-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56097-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="56097-207">Additional resources</span></span>

* [<span data-ttu-id="56097-208">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="56097-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="56097-209">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="56097-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="56097-210">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="56097-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="56097-211">中心</span><span class="sxs-lookup"><span data-stu-id="56097-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="56097-212">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="56097-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="56097-213">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="56097-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="56097-214">跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="56097-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="56097-215">Azure SignalR 服务无服务器文档</span><span class="sxs-lookup"><span data-stu-id="56097-215">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
