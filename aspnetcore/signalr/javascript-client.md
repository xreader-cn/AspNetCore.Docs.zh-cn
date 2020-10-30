---
title: 'ASP.NET Core :::no-loc(SignalR)::: JavaScript 客户端'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: JavaScript 客户端概述。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/javascript-client
ms.openlocfilehash: b4b1bc6131a6676710adbf2503efe3f304d89a58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050844"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="e8e6b-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="e8e6b-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8e6b-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="e8e6b-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="e8e6b-105">ASP.NET Core :::no-loc(SignalR)::: JavaScript 客户端库使开发人员能够调用服务器端集线器代码。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-105">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="e8e6b-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e8e6b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="e8e6b-107">安装 :::no-loc(SignalR)::: 客户端包</span><span class="sxs-lookup"><span data-stu-id="e8e6b-107">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="e8e6b-108">:::no-loc(SignalR):::JavaScript 客户端库以[npm](https://www.npmjs.com/)包的形式提供。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-108">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="e8e6b-109">以下部分概述了安装客户端库的不同方式。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="e8e6b-110">通过 npm 安装</span><span class="sxs-lookup"><span data-stu-id="e8e6b-110">Install with npm</span></span>

<span data-ttu-id="e8e6b-111">对于 Visual Studio，请在根文件夹中的 " **包管理器控制台** " 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="e8e6b-112">对于 Visual Studio Code，请从 **集成终端** 运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="e8e6b-113">npm 将包内容安装到 *node_modules \\ @microsoft\signalr\dist\browser* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="e8e6b-114">在 *wwwroot \\ lib* 文件夹下创建名为 *signalr* 的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="e8e6b-115">将 *signalr.js* 文件复制到 *wwwroot\lib\signalr* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="e8e6b-116">:::no-loc(SignalR):::在元素中引用 JavaScript 客户端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-116">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="e8e6b-117">例如： 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="e8e6b-118">使用内容交付网络 (CDN) </span><span class="sxs-lookup"><span data-stu-id="e8e6b-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="e8e6b-119">若要在不使用 npm 先决条件的情况下使用客户端库，请引用 CDN 托管的客户端库副本。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="e8e6b-120">例如： 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="e8e6b-121">以下 Cdn 提供了客户端库：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="e8e6b-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="e8e6b-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="e8e6b-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="e8e6b-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="e8e6b-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="e8e6b-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="e8e6b-125">通过 LibMan 安装</span><span class="sxs-lookup"><span data-stu-id="e8e6b-125">Install with LibMan</span></span>

<span data-ttu-id="e8e6b-126">[LibMan](xref:client-side/libman/index) 可用于从 CDN 托管的客户端库安装特定的客户端库文件。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="e8e6b-127">例如，仅将缩小 JavaScript 文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="e8e6b-128">有关该方法的详细信息，请参阅 [添加 :::no-loc(SignalR)::: 客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-128">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="e8e6b-129">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="e8e6b-129">Connect to a hub</span></span>

<span data-ttu-id="e8e6b-130">下面的代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="e8e6b-131">中心名称不区分大小写：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="e8e6b-132">跨域连接</span><span class="sxs-lookup"><span data-stu-id="e8e6b-132">Cross-origin connections</span></span>

<span data-ttu-id="e8e6b-133">通常，浏览器从与请求的页相同的域中加载连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="e8e6b-134">但是，在某些情况下，需要与另一个域建立连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-134">However, there are occasions when a connection to another domain is required.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e8e6b-135">客户端代码必须使用绝对 URL，而不是相对 URL。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-135">The client code must use an absolute URL instead of a relative URL.</span></span> <span data-ttu-id="e8e6b-136">将 `.withUrl("/chathub")` 更改为 `.withUrl("https://myappurl/chathub")`。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-136">Change `.withUrl("/chathub")` to `.withUrl("https://myappurl/chathub")`.</span></span>

<span data-ttu-id="e8e6b-137">为了防止恶意站点读取其他站点中的敏感数据，默认情况下会禁用 [跨域连接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-137">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="e8e6b-138">若要允许跨源请求，请在类中启用该请求 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-138">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="e8e6b-139">从客户端调用中心方法</span><span class="sxs-lookup"><span data-stu-id="e8e6b-139">Call hub methods from the client</span></span>

<span data-ttu-id="e8e6b-140">JavaScript 客户端通过[HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)的[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)方法在集线器上调用公共方法。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-140">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="e8e6b-141">此 `invoke` 方法接受：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-141">The `invoke` method accepts:</span></span>

* <span data-ttu-id="e8e6b-142">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-142">The name of the hub method.</span></span>
* <span data-ttu-id="e8e6b-143">在 hub 方法中定义的所有参数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-143">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="e8e6b-144">在下面的示例中，中心的方法名称是 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-144">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="e8e6b-145">传递给 `invoke` 集线器方法和参数的第二个和第三个参数 `user` `message` ：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-145">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="e8e6b-146">仅 :::no-loc(SignalR)::: 在 *默认* 模式下使用 Azure 服务时，才支持从客户端调用中心方法。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-146">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="e8e6b-147">有关详细信息，请参阅 [Signalr GitHub 存储库)  (常见问题解答 ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-147">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="e8e6b-148">`invoke`方法返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-148">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="e8e6b-149">`Promise`当服务器上的方法返回时，将用返回值解析)  (。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-149">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="e8e6b-150">如果服务器上的方法引发错误，将拒绝， `Promise` 并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-150">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e8e6b-151">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法来处理这些情况。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-151">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="e8e6b-152">JavaScript 客户端也可以通过的 [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 方法在集线器上调用公共方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-152">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="e8e6b-153">与 `invoke` 方法不同， `send` 方法不会等待服务器的响应。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-153">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="e8e6b-154">`send`方法返回 JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-154">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="e8e6b-155">`Promise`当消息已发送到服务器时，将解决。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-155">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="e8e6b-156">如果发送消息时出错，将 `Promise` 拒绝，并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-156">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e8e6b-157">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法来处理这些情况。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-157">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="e8e6b-158">使用 `send` 不会等到服务器收到消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-158">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="e8e6b-159">因此，不可能从服务器返回数据或错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-159">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="e8e6b-160">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="e8e6b-160">Call client methods from the hub</span></span>

<span data-ttu-id="e8e6b-161">若要从中心接收消息，请使用的 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 方法定义方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-161">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="e8e6b-162">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-162">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="e8e6b-163">集线器传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-163">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="e8e6b-164">在下面的示例中，方法名称是 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-164">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="e8e6b-165">参数名称为 `user` 和 `message` ：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-165">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="e8e6b-166">`connection.on`当服务器端代码使用方法调用时，上面的代码会运行 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-166">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="e8e6b-167">:::no-loc(SignalR)::: 通过匹配和中定义的方法名称和参数，确定要调用的客户端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-167">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="e8e6b-168">作为最佳做法，请在后面调用 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-168">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="e8e6b-169">这样做可确保在收到消息之前注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-169">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="e8e6b-170">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="e8e6b-170">Error handling and logging</span></span>

<span data-ttu-id="e8e6b-171">使用和，使用 `try` `catch` `async` 和 `await` 或 `Promise` 的 `catch` 方法来处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-171">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="e8e6b-172">使用 `console.error` 将错误输出到浏览器控制台：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-172">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="e8e6b-173">设置客户端日志跟踪，方法是在建立连接时将记录器和事件类型传递给日志。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-173">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="e8e6b-174">记录的消息具有指定的日志级别和更高的日志级别。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-174">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="e8e6b-175">可用的日志级别如下所示：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-175">Available log levels are as follows:</span></span>

* <span data-ttu-id="e8e6b-176">`signalR.LogLevel.Error`：错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-176">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="e8e6b-177">`Error`仅记录消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-177">Logs `Error` messages only.</span></span>
* <span data-ttu-id="e8e6b-178">`signalR.LogLevel.Warning`：有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-178">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="e8e6b-179">日志 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-179">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e8e6b-180">`signalR.LogLevel.Information`：无错误的状态消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-180">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="e8e6b-181">日志 `Information` 、 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-181">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e8e6b-182">`signalR.LogLevel.Trace`：跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-182">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="e8e6b-183">记录所有内容，包括中心和客户端之间传输的数据。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-183">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="e8e6b-184">使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-184">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="e8e6b-185">消息记录到浏览器控制台：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-185">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="e8e6b-186">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="e8e6b-186">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="e8e6b-187">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="e8e6b-187">Automatically reconnect</span></span>

<span data-ttu-id="e8e6b-188">的 JavaScript 客户端 :::no-loc(SignalR)::: 可以配置为使用 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-188">The JavaScript client for :::no-loc(SignalR)::: can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="e8e6b-189">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-189">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="e8e6b-190">在没有任何参数的情况下，会 `withAutomaticReconnect()` 将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-190">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="e8e6b-191">在开始任何重新连接尝试之前， `HubConnection` 将转换为 `HubConnectionState.Reconnecting` 状态，并激发其 `onreconnecting` 回调，而不是转换为 `Disconnected` 状态，并触发其 `onclose` 回调，如 `HubConnection` 不配置自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-191">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="e8e6b-192">这为用户提供警告连接已丢失并禁用 UI 元素的机会。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-192">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e8e6b-193">如果客户端在其前四次尝试内成功重新连接，则 `HubConnection` 将转换回 `Connected` 状态并激发其 `onreconnected` 回调。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-193">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="e8e6b-194">这为用户提供了通知用户连接已重新建立的机会。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-194">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="e8e6b-195">由于连接在服务器上看起来是全新的，因此 `connectionId` 将向回调提供一个新的 `onreconnected` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-195">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="e8e6b-196">`onreconnected` `connectionId` 如果 `HubConnection` 配置为[跳过协商](xref:signalr/configuration#configure-client-options)，则不会定义回调的参数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-196">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e8e6b-197">`withAutomaticReconnect()` 不会将配置 `HubConnection` 为重试初始启动失败，因此，需要手动处理启动失败：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-197">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log(":::no-loc(SignalR)::: Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="e8e6b-198">如果客户端在其前四次尝试中未成功重新连接，则 `HubConnection` 将转换为 `Disconnected` 状态并激发其 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 回调。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="e8e6b-199">这为用户提供了通知用户连接永久丢失的机会，并建议刷新页面：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-199">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="e8e6b-200">若要在断开连接或更改重新连接时间安排之前配置自定义的重新连接尝试次数，请 `withAutomaticReconnect` 接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-200">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="e8e6b-201">前面的示例将配置 `HubConnection` 为在连接丢失后立即开始尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-201">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="e8e6b-202">这也适用于默认配置。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-202">This is also true for the default configuration.</span></span>

<span data-ttu-id="e8e6b-203">如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-203">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="e8e6b-204">如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-204">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="e8e6b-205">然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离，而不是在另一个30秒内尝试再次尝试重新连接，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-205">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="e8e6b-206">如果需要更好地控制计时和自动重新连接尝试的次数，则 `withAutomaticReconnect` 接受一个实现接口的 `IRetryPolicy` 对象，该对象具有一个名为的方法 `nextRetryDelayInMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-206">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="e8e6b-207">`nextRetryDelayInMilliseconds` 采用类型为的单个自变量 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-207">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="e8e6b-208">`RetryContext`具有三个属性： `previousRetryCount` `elapsedMilliseconds` 和分别为 `retryReason` `number` 、 `number` 和 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-208">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="e8e6b-209">第一次重新连接尝试之前， `previousRetryCount` 和都 `elapsedMilliseconds` 是零， `retryReason` 将是导致连接丢失的错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-209">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="e8e6b-210">每次失败的重试次数递增一次后，将进行 `previousRetryCount` `elapsedMilliseconds` 更新，以反映到目前为止的重新连接所用的时间（以毫秒为单位），并且 `retryReason` 将是导致上次重新连接尝试失败的错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-210">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="e8e6b-211">`nextRetryDelayInMilliseconds` 必须返回一个数字，该数字表示在下一次重新连接尝试之前要等待的毫秒数，或者 `null` ，如果应停止重新连接，则为 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-211">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="e8e6b-212">或者，你可以编写将手动重新连接客户端的代码，如 [手动重新连接](#manually-reconnect)中所示。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-212">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="e8e6b-213">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="e8e6b-213">Manually reconnect</span></span>

<span data-ttu-id="e8e6b-214">下面的代码演示典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-214">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="e8e6b-215">函数 (在这种情况下，将 `start` 创建函数) 来启动连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-215">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="e8e6b-216">`start`在连接的 `onclose` 事件处理程序中调用函数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-216">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="e8e6b-217">实际的实现将使用指数回退或在放弃之前重试指定的次数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-217">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8e6b-218">其他资源</span><span class="sxs-lookup"><span data-stu-id="e8e6b-218">Additional resources</span></span>

* [<span data-ttu-id="e8e6b-219">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="e8e6b-219">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="e8e6b-220">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="e8e6b-220">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e8e6b-221">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="e8e6b-221">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="e8e6b-222">中心</span><span class="sxs-lookup"><span data-stu-id="e8e6b-222">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e8e6b-223">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="e8e6b-223">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e8e6b-224">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="e8e6b-224">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="e8e6b-225"> (CORS 的跨源请求) </span><span class="sxs-lookup"><span data-stu-id="e8e6b-225">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="e8e6b-226">Azure :::no-loc(SignalR)::: Service 无服务器文档</span><span class="sxs-lookup"><span data-stu-id="e8e6b-226">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="e8e6b-227">排查连接错误</span><span class="sxs-lookup"><span data-stu-id="e8e6b-227">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8e6b-228">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="e8e6b-228">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="e8e6b-229">ASP.NET Core :::no-loc(SignalR)::: JavaScript 客户端库使开发人员能够调用服务器端集线器代码。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-229">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="e8e6b-230">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e8e6b-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="e8e6b-231">安装 :::no-loc(SignalR)::: 客户端包</span><span class="sxs-lookup"><span data-stu-id="e8e6b-231">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="e8e6b-232">:::no-loc(SignalR):::JavaScript 客户端库以[npm](https://www.npmjs.com/)包的形式提供。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-232">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="e8e6b-233">以下部分概述了安装客户端库的不同方式。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-233">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="e8e6b-234">通过 npm 安装</span><span class="sxs-lookup"><span data-stu-id="e8e6b-234">Install with npm</span></span>

<span data-ttu-id="e8e6b-235">如果使用的是 Visual Studio，请在根文件夹中的 " **包管理器控制台** " 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-235">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="e8e6b-236">对于 Visual Studio Code，请从 **集成终端** 运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-236">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="e8e6b-237">npm 将包内容安装到 *node_modules \\ @aspnet\signalr\dist\browser* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-237">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="e8e6b-238">在 *wwwroot \\ lib* 文件夹下创建名为 *signalr* 的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-238">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="e8e6b-239">将 *signalr.js* 文件复制到 *wwwroot\lib\signalr* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-239">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="e8e6b-240">:::no-loc(SignalR):::在元素中引用 JavaScript 客户端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-240">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="e8e6b-241">例如： 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-241">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="e8e6b-242">使用内容交付网络 (CDN) </span><span class="sxs-lookup"><span data-stu-id="e8e6b-242">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="e8e6b-243">若要在不使用 npm 先决条件的情况下使用客户端库，请引用 CDN 托管的客户端库副本。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-243">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="e8e6b-244">例如： 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-244">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="e8e6b-245">以下 Cdn 提供了客户端库：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-245">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="e8e6b-246">cdnjs</span><span class="sxs-lookup"><span data-stu-id="e8e6b-246">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="e8e6b-247">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="e8e6b-247">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="e8e6b-248">unpkg</span><span class="sxs-lookup"><span data-stu-id="e8e6b-248">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="e8e6b-249">通过 LibMan 安装</span><span class="sxs-lookup"><span data-stu-id="e8e6b-249">Install with LibMan</span></span>

<span data-ttu-id="e8e6b-250">[LibMan](xref:client-side/libman/index) 可用于从 CDN 托管的客户端库安装特定的客户端库文件。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-250">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="e8e6b-251">例如，仅将缩小 JavaScript 文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-251">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="e8e6b-252">有关该方法的详细信息，请参阅 [添加 :::no-loc(SignalR)::: 客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-252">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="e8e6b-253">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="e8e6b-253">Connect to a hub</span></span>

<span data-ttu-id="e8e6b-254">下面的代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-254">The following code creates and starts a connection.</span></span> <span data-ttu-id="e8e6b-255">中心名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-255">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="e8e6b-256">跨域连接</span><span class="sxs-lookup"><span data-stu-id="e8e6b-256">Cross-origin connections</span></span>

<span data-ttu-id="e8e6b-257">通常，浏览器从与请求的页相同的域中加载连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-257">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="e8e6b-258">但是，在某些情况下，需要与另一个域建立连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-258">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="e8e6b-259">为了防止恶意站点读取其他站点中的敏感数据，默认情况下会禁用 [跨域连接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-259">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="e8e6b-260">若要允许跨源请求，请在类中启用它 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-260">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="e8e6b-261">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="e8e6b-261">Call hub methods from client</span></span>

<span data-ttu-id="e8e6b-262">JavaScript 客户端通过[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-262">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="e8e6b-263">此 `invoke` 方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-263">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="e8e6b-264">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-264">The name of the hub method.</span></span> <span data-ttu-id="e8e6b-265">在下面的示例中，中心的方法名称是 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-265">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="e8e6b-266">在 hub 方法中定义的所有参数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-266">Any arguments defined in the hub method.</span></span> <span data-ttu-id="e8e6b-267">在下面的示例中，自变量名称为 `message` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-267">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="e8e6b-268">示例代码使用了在所有主要浏览器（Internet Explorer 除外）的当前版本中受支持的箭头函数语法。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-268">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="e8e6b-269">仅 :::no-loc(SignalR)::: 在 *默认* 模式下使用 Azure 服务时，才支持从客户端调用中心方法。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-269">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="e8e6b-270">有关详细信息，请参阅 [Signalr GitHub 存储库)  (常见问题解答 ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-270">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="e8e6b-271">`invoke`方法返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-271">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="e8e6b-272">`Promise`当服务器上的方法返回时，将用返回值解析)  (。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-272">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="e8e6b-273">如果服务器上的方法引发错误，将拒绝， `Promise` 并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-273">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e8e6b-274">使用 `then` 和 `catch` 方法 `Promise` 来处理这些事例 (或 `await` 语法) 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-274">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="e8e6b-275">`send`方法返回 JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-275">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="e8e6b-276">`Promise`当消息已发送到服务器时，将解决。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-276">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="e8e6b-277">如果发送消息时出错，将 `Promise` 拒绝，并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-277">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="e8e6b-278">使用 `then` 和 `catch` 方法 `Promise` 来处理这些事例 (或 `await` 语法) 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-278">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="e8e6b-279">使用 `send` 不会等到服务器收到消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-279">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="e8e6b-280">因此，不可能从服务器返回数据或错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-280">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="e8e6b-281">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="e8e6b-281">Call client methods from hub</span></span>

<span data-ttu-id="e8e6b-282">若要从中心接收消息，请使用的 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 方法定义方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-282">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="e8e6b-283">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-283">The name of the JavaScript client method.</span></span> <span data-ttu-id="e8e6b-284">在下面的示例中，方法名称是 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-284">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="e8e6b-285">集线器传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-285">Arguments the hub passes to the method.</span></span> <span data-ttu-id="e8e6b-286">在下面的示例中，参数值为 `message` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-286">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="e8e6b-287">`connection.on`当服务器端代码使用方法调用时，上面的代码将运行 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-287">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="e8e6b-288">:::no-loc(SignalR)::: 通过匹配和中定义的方法名称和参数，确定要调用的客户端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-288">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="e8e6b-289">作为最佳做法，请在后面调用 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-289">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="e8e6b-290">这样做可确保在收到消息之前注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-290">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="e8e6b-291">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="e8e6b-291">Error handling and logging</span></span>

<span data-ttu-id="e8e6b-292">将 `catch` 方法链接到方法的末尾 `start` ，以处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-292">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="e8e6b-293">使用 `console.error` 将错误输出到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-293">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="e8e6b-294">设置客户端日志跟踪，方法是在建立连接时将记录器和事件类型传递给日志。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-294">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="e8e6b-295">记录的消息具有指定的日志级别和更高的日志级别。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-295">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="e8e6b-296">可用的日志级别如下所示：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-296">Available log levels are as follows:</span></span>

* <span data-ttu-id="e8e6b-297">`signalR.LogLevel.Error`：错误消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-297">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="e8e6b-298">`Error`仅记录消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-298">Logs `Error` messages only.</span></span>
* <span data-ttu-id="e8e6b-299">`signalR.LogLevel.Warning`：有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-299">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="e8e6b-300">日志 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-300">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e8e6b-301">`signalR.LogLevel.Information`：无错误的状态消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-301">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="e8e6b-302">日志 `Information` 、 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-302">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="e8e6b-303">`signalR.LogLevel.Trace`：跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-303">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="e8e6b-304">记录所有内容，包括中心和客户端之间传输的数据。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-304">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="e8e6b-305">使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-305">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="e8e6b-306">消息将记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-306">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="e8e6b-307">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="e8e6b-307">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="e8e6b-308">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="e8e6b-308">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="e8e6b-309">在3.0 之前， :::no-loc(SignalR)::: 不会自动重新连接 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-309">Prior to 3.0, the JavaScript client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="e8e6b-310">必须编写代码来手动重新连接客户端。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-310">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="e8e6b-311">下面的代码演示典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="e8e6b-311">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="e8e6b-312">函数 (在这种情况下，将 `start` 创建函数) 来启动连接。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-312">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="e8e6b-313">`start`在连接的 `onclose` 事件处理程序中调用函数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-313">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="e8e6b-314">实际的实现将使用指数回退或在放弃之前重试指定的次数。</span><span class="sxs-lookup"><span data-stu-id="e8e6b-314">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8e6b-315">其他资源</span><span class="sxs-lookup"><span data-stu-id="e8e6b-315">Additional resources</span></span>

* [<span data-ttu-id="e8e6b-316">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="e8e6b-316">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="e8e6b-317">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="e8e6b-317">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e8e6b-318">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="e8e6b-318">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="e8e6b-319">中心</span><span class="sxs-lookup"><span data-stu-id="e8e6b-319">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e8e6b-320">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="e8e6b-320">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e8e6b-321">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="e8e6b-321">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="e8e6b-322"> (CORS 的跨源请求) </span><span class="sxs-lookup"><span data-stu-id="e8e6b-322">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="e8e6b-323">Azure :::no-loc(SignalR)::: Service 无服务器文档</span><span class="sxs-lookup"><span data-stu-id="e8e6b-323">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
