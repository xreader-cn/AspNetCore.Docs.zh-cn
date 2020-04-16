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
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440852"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="e9510-103">ASP.NET核心SignalRJavaScript客户端</span><span class="sxs-lookup"><span data-stu-id="e9510-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="e9510-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="e9510-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="e9510-105">ASP.NET核心SignalRJavaScript 客户端库使开发人员能够调用服务器端集线器代码。</span><span class="sxs-lookup"><span data-stu-id="e9510-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="e9510-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e9510-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="e9510-107">安装SignalR客户端包</span><span class="sxs-lookup"><span data-stu-id="e9510-107">Install the SignalR client package</span></span>

<span data-ttu-id="e9510-108">JavaScriptSignalR客户端库作为[npm](https://www.npmjs.com/)包提供。</span><span class="sxs-lookup"><span data-stu-id="e9510-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="e9510-109">以下各节概述了安装客户端库的不同方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="e9510-110">使用 npm 安装</span><span class="sxs-lookup"><span data-stu-id="e9510-110">Install with npm</span></span>

<span data-ttu-id="e9510-111">如果使用 Visual Studio，则在根文件夹中运行**包管理器控制台**中的以下命令。</span><span class="sxs-lookup"><span data-stu-id="e9510-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="e9510-112">对于可视化工作室代码，请从**集成终端**运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e9510-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="e9510-113">npm 在*node_modules\\*文件夹中安装包内容。</span><span class="sxs-lookup"><span data-stu-id="e9510-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="e9510-114">在*wwwroot\\lib*文件夹下创建名为*信号器*的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="e9510-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="e9510-115">将*Signalr.js*文件复制到*wwwroot_lib_signalr*文件夹。</span><span class="sxs-lookup"><span data-stu-id="e9510-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="e9510-116">npm 在*node_modules\\*文件夹中安装包内容。</span><span class="sxs-lookup"><span data-stu-id="e9510-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="e9510-117">在*wwwroot\\lib*文件夹下创建名为*信号器*的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="e9510-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="e9510-118">将*Signalr.js*文件复制到*wwwroot_lib_signalr*文件夹。</span><span class="sxs-lookup"><span data-stu-id="e9510-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="e9510-119">引用`<script>`元素SignalR中的 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="e9510-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="e9510-120">例如：</span><span class="sxs-lookup"><span data-stu-id="e9510-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="e9510-121">使用内容交付网络 （CDN）</span><span class="sxs-lookup"><span data-stu-id="e9510-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="e9510-122">要使用没有 npm 先决条件的客户端库，请引用客户端库的 CDN 托管副本。</span><span class="sxs-lookup"><span data-stu-id="e9510-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="e9510-123">例如：</span><span class="sxs-lookup"><span data-stu-id="e9510-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="e9510-124">客户端库在以下 CDN 上可用：</span><span class="sxs-lookup"><span data-stu-id="e9510-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="e9510-125">恩杰斯</span><span class="sxs-lookup"><span data-stu-id="e9510-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="e9510-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="e9510-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="e9510-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="e9510-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="e9510-128">恩杰斯</span><span class="sxs-lookup"><span data-stu-id="e9510-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="e9510-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="e9510-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="e9510-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="e9510-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="e9510-131">使用 LibMan 安装</span><span class="sxs-lookup"><span data-stu-id="e9510-131">Install with LibMan</span></span>

<span data-ttu-id="e9510-132">[LibMan](xref:client-side/libman/index)可用于从 CDN 托管的客户端库安装特定的客户端库文件。</span><span class="sxs-lookup"><span data-stu-id="e9510-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="e9510-133">例如，仅将已小的 JavaScript 文件添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="e9510-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="e9510-134">有关该方法的详细信息，请参阅[SignalR添加客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="e9510-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="e9510-135">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="e9510-135">Connect to a hub</span></span>

<span data-ttu-id="e9510-136">以下代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="e9510-137">中心的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="e9510-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="e9510-138">交叉源连接</span><span class="sxs-lookup"><span data-stu-id="e9510-138">Cross-origin connections</span></span>

<span data-ttu-id="e9510-139">通常，浏览器将连接与请求的页面从同一域加载。</span><span class="sxs-lookup"><span data-stu-id="e9510-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="e9510-140">但是，有时需要连接到另一个域。</span><span class="sxs-lookup"><span data-stu-id="e9510-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="e9510-141">为了防止恶意站点从其他站点读取敏感数据，默认情况下将禁用[跨源连接](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="e9510-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="e9510-142">要允许跨源请求，请在类中`Startup`启用它。</span><span class="sxs-lookup"><span data-stu-id="e9510-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="e9510-143">从客户端调用中心方法</span><span class="sxs-lookup"><span data-stu-id="e9510-143">Call hub methods from client</span></span>

<span data-ttu-id="e9510-144">JavaScript客户端通过[HubConnect](/javascript/api/%40aspnet/signalr/hubconnection)的[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="e9510-145">该方法`invoke`接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="e9510-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="e9510-146">中心方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e9510-146">The name of the hub method.</span></span> <span data-ttu-id="e9510-147">在下面的示例中，中心上的方法名称为`SendMessage`。</span><span class="sxs-lookup"><span data-stu-id="e9510-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="e9510-148">在中心方法中定义的任何参数。</span><span class="sxs-lookup"><span data-stu-id="e9510-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="e9510-149">在下面的示例中，参数名称为`message`。</span><span class="sxs-lookup"><span data-stu-id="e9510-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="e9510-150">示例代码使用除 Internet Explorer 以外的所有主要浏览器的当前版本中支持的箭头函数语法。</span><span class="sxs-lookup"><span data-stu-id="e9510-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="e9510-151">如果在*无服务器模式下*使用SignalRAzure 服务，则无法从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="e9510-152">有关详细信息，请参阅[SignalR服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="e9510-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="e9510-153">该方法`invoke`返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="e9510-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="e9510-154">当`Promise`服务器上的方法返回时，使用返回值（如果有）解析 。</span><span class="sxs-lookup"><span data-stu-id="e9510-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="e9510-155">如果服务器上的方法引发错误，`Promise`则 拒绝使用错误消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e9510-156">使用`then`本身`catch`上的`Promise`和 方法来处理这些情况（`await`或语法）。</span><span class="sxs-lookup"><span data-stu-id="e9510-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="e9510-157">该方法`send`返回JavaScript。 `Promise`</span><span class="sxs-lookup"><span data-stu-id="e9510-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="e9510-158">将`Promise`解解消息发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="e9510-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="e9510-159">如果发送消息有错误，`Promise`则 拒绝使用错误消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e9510-160">使用`then`本身`catch`上的`Promise`和 方法来处理这些情况（`await`或语法）。</span><span class="sxs-lookup"><span data-stu-id="e9510-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="e9510-161">使用`send`不会等到服务器收到消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="e9510-162">因此，无法从服务器返回数据或错误。</span><span class="sxs-lookup"><span data-stu-id="e9510-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="e9510-163">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="e9510-163">Call client methods from hub</span></span>

<span data-ttu-id="e9510-164">要从集线器接收消息，请使用 的[on](/javascript/api/%40aspnet/signalr/hubconnection#on)方法定义`HubConnection`方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="e9510-165">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e9510-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="e9510-166">在下面的示例中，方法名称为`ReceiveMessage`。</span><span class="sxs-lookup"><span data-stu-id="e9510-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="e9510-167">中心传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="e9510-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="e9510-168">在下面的示例中，参数值为`message`。</span><span class="sxs-lookup"><span data-stu-id="e9510-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="e9510-169">当服务器端代码`connection.on`使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法调用它时，前面的代码将运行。</span><span class="sxs-lookup"><span data-stu-id="e9510-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="e9510-170">通过匹配 和 中`SendAsync`定义的方法名称和参数来确定要调用的客户端方法`connection.on`。</span><span class="sxs-lookup"><span data-stu-id="e9510-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="e9510-171">最佳做法是调用 后`HubConnection``on`上的[开始](/javascript/api/%40aspnet/signalr/hubconnection#start)方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="e9510-172">这样做可确保在收到任何消息之前注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="e9510-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="e9510-173">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="e9510-173">Error handling and logging</span></span>

<span data-ttu-id="e9510-174">将`catch`方法链接到方法的末尾，`start`以处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="e9510-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="e9510-175">用于`console.error`将错误输出到浏览器的控制台。</span><span class="sxs-lookup"><span data-stu-id="e9510-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="e9510-176">通过传递记录器和事件类型来在建立连接时记录客户端日志跟踪。</span><span class="sxs-lookup"><span data-stu-id="e9510-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="e9510-177">消息记录与指定的日志级别和更高。</span><span class="sxs-lookup"><span data-stu-id="e9510-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="e9510-178">可用日志级别如下：</span><span class="sxs-lookup"><span data-stu-id="e9510-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="e9510-179">`signalR.LogLevel.Error`&ndash;错误消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="e9510-180">仅`Error`记录邮件。</span><span class="sxs-lookup"><span data-stu-id="e9510-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="e9510-181">`signalR.LogLevel.Warning`&ndash;有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="e9510-182">日志`Warning`和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e9510-183">`signalR.LogLevel.Information`&ndash;状态消息，无错误。</span><span class="sxs-lookup"><span data-stu-id="e9510-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="e9510-184">日志`Information``Warning`和`Error`消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e9510-185">`signalR.LogLevel.Trace`&ndash;跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="e9510-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="e9510-186">记录所有内容，包括集线器和客户端之间传输的数据。</span><span class="sxs-lookup"><span data-stu-id="e9510-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="e9510-187">使用[HubConnectBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[配置日志记录](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="e9510-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="e9510-188">消息将记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="e9510-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="e9510-189">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="e9510-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="e9510-190">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="e9510-190">Automatically reconnect</span></span>

<span data-ttu-id="e9510-191">JavaScriptSignalR客户端可以配置为使用`withAutomaticReconnect`[HubConnectBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="e9510-192">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="e9510-193">没有任何参数，`withAutomaticReconnect()`将客户端配置为分别等待 0、2、10 和 30 秒，然后再尝试每次重新连接尝试，在四次尝试失败后停止。</span><span class="sxs-lookup"><span data-stu-id="e9510-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="e9510-194">在开始任何重新连接尝试之前，`HubConnection``HubConnectionState.Reconnecting`将转换为状态`onreconnecting`并触发其回调，而不是转换到`Disconnected`状态并触发其`onclose`回调，就像未配置自动重新连接一`HubConnection`样。</span><span class="sxs-lookup"><span data-stu-id="e9510-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="e9510-195">这提供了一个机会来警告用户连接已丢失并禁用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="e9510-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e9510-196">如果客户端在前四次尝试中成功重新连接，则 将`HubConnection`转换回`Connected`状态并触发其`onreconnected`回调。</span><span class="sxs-lookup"><span data-stu-id="e9510-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="e9510-197">这提供了一个机会，通知用户连接已重新建立。</span><span class="sxs-lookup"><span data-stu-id="e9510-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="e9510-198">由于连接对服务器看起来完全新，因此将为`connectionId``onreconnected`回调提供新的连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="e9510-199">如果`onreconnected`配置为[跳过协商](xref:signalr/configuration#configure-client-options) `connectionId` ，`HubConnection`则回调的参数将未定义。</span><span class="sxs-lookup"><span data-stu-id="e9510-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e9510-200">`withAutomaticReconnect()`不会配置 以`HubConnection`重试初始启动失败，因此需要手动处理启动失败：</span><span class="sxs-lookup"><span data-stu-id="e9510-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="e9510-201">如果客户端在前四次尝试中未成功重新连接，则 将`HubConnection`转换为`Disconnected`状态并触发其[关闭](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。</span><span class="sxs-lookup"><span data-stu-id="e9510-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="e9510-202">这提供了一个机会，通知用户连接已永久丢失，并建议刷新页面：</span><span class="sxs-lookup"><span data-stu-id="e9510-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e9510-203">为了在断开连接或更改重新连接计时之前配置自定义的重新连接尝试次数，`withAutomaticReconnect`请接受表示延迟（以毫秒为单位）的数字数组，以等待，然后再开始每次重新连接尝试。</span><span class="sxs-lookup"><span data-stu-id="e9510-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="e9510-204">前面的示例配置 要`HubConnection`在连接丢失后立即开始尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="e9510-205">对于默认配置也是如此。</span><span class="sxs-lookup"><span data-stu-id="e9510-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="e9510-206">如果第一次重新连接尝试失败，第二次重新连接尝试也将立即开始，而不是像默认配置中那样等待 2 秒。</span><span class="sxs-lookup"><span data-stu-id="e9510-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="e9510-207">如果第二次重新连接尝试失败，第三次重新连接尝试将在 10 秒内开始，这再次类似于默认配置。</span><span class="sxs-lookup"><span data-stu-id="e9510-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="e9510-208">然后，自定义行为会在第三次重新连接尝试失败后停止，而不是像在默认配置中那样在 30 秒内尝试再次重新连接尝试，从而再次偏离默认行为。</span><span class="sxs-lookup"><span data-stu-id="e9510-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="e9510-209">如果希望对自动重新连接尝试的计时和次数进行更多控制，请`withAutomaticReconnect`接受实现`IRetryPolicy`接口的对象，该对象具有名为 的`nextRetryDelayInMilliseconds`单个方法。</span><span class="sxs-lookup"><span data-stu-id="e9510-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="e9510-210">`nextRetryDelayInMilliseconds`使用 类型`RetryContext`获取单个参数。</span><span class="sxs-lookup"><span data-stu-id="e9510-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="e9510-211">有`RetryContext`三个属性： `previousRetryCount``elapsedMilliseconds`和`retryReason`分别为`number`a`number`和`Error`。</span><span class="sxs-lookup"><span data-stu-id="e9510-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="e9510-212">在第一次重新连接尝试之前，`previousRetryCount`两`elapsedMilliseconds`者都将为零，`retryReason`并且将是导致连接丢失的错误。</span><span class="sxs-lookup"><span data-stu-id="e9510-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="e9510-213">每次失败的重试尝试（`previousRetryCount`将递增为 1）后，`elapsedMilliseconds`将更新以反映到目前为止重新连接所花费的时间（以毫秒为单位），`retryReason`并且将是导致上次重新连接尝试失败的错误。</span><span class="sxs-lookup"><span data-stu-id="e9510-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="e9510-214">`nextRetryDelayInMilliseconds`必须返回表示在下一次重新连接尝试之前等待的毫秒数的数字，`null``HubConnection`或者是否应停止重新连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="e9510-215">或者，您可以编写代码，这些代码将手动重新连接客户端，如[手动重新连接](#manually-reconnect)中所示。</span><span class="sxs-lookup"><span data-stu-id="e9510-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="e9510-216">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="e9510-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="e9510-217">在 3.0 之前，的SignalRJavaScript 客户端不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="e9510-218">您必须编写代码，以手动重新连接客户端。</span><span class="sxs-lookup"><span data-stu-id="e9510-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="e9510-219">以下代码演示了典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="e9510-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="e9510-220">创建函数（在本例中为`start`函数）以启动连接。</span><span class="sxs-lookup"><span data-stu-id="e9510-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="e9510-221">在`start`连接`onclose`的事件处理程序中调用函数。</span><span class="sxs-lookup"><span data-stu-id="e9510-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="e9510-222">实际实现将使用指数级回退或在放弃之前重试指定次数。</span><span class="sxs-lookup"><span data-stu-id="e9510-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9510-223">其他资源</span><span class="sxs-lookup"><span data-stu-id="e9510-223">Additional resources</span></span>

* [<span data-ttu-id="e9510-224">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="e9510-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="e9510-225">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="e9510-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e9510-226">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="e9510-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="e9510-227">枢纽</span><span class="sxs-lookup"><span data-stu-id="e9510-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e9510-228">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="e9510-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e9510-229">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="e9510-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="e9510-230">跨源请求 （CORS）</span><span class="sxs-lookup"><span data-stu-id="e9510-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="e9510-231">[AzureSignalR服务无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="e9510-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
