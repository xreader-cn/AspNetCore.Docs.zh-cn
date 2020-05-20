---
<span data-ttu-id="56327-101">标题： "ASP.NET Core SignalR javascript 客户端" author：说明： "ASP.NET Core SignalR JavaScript 客户端概述"。</span><span class="sxs-lookup"><span data-stu-id="56327-101">title: 'ASP.NET Core SignalR JavaScript client' author: description: 'Overview of ASP.NET Core SignalR JavaScript client.'</span></span>
<span data-ttu-id="56327-102">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="56327-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56327-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56327-103">'Blazor'</span></span>
- <span data-ttu-id="56327-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56327-104">'Identity'</span></span>
- <span data-ttu-id="56327-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56327-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="56327-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56327-106">'Razor'</span></span>
- <span data-ttu-id="56327-107">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="56327-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="56327-108">ASP.NET Core SignalR JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="56327-108">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="56327-109">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="56327-109">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="56327-110">ASP.NET Core SignalR JavaScript 客户端库使开发人员能够调用服务器端集线器代码。</span><span class="sxs-lookup"><span data-stu-id="56327-110">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="56327-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="56327-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="56327-112">安装 SignalR 客户端包</span><span class="sxs-lookup"><span data-stu-id="56327-112">Install the SignalR client package</span></span>

<span data-ttu-id="56327-113">SignalRJavaScript 客户端库以[npm](https://www.npmjs.com/)包的形式提供。</span><span class="sxs-lookup"><span data-stu-id="56327-113">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="56327-114">以下部分概述了安装客户端库的不同方式。</span><span class="sxs-lookup"><span data-stu-id="56327-114">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="56327-115">通过 npm 安装</span><span class="sxs-lookup"><span data-stu-id="56327-115">Install with npm</span></span>

<span data-ttu-id="56327-116">如果使用的是 Visual Studio，请在根文件夹中的 "**包管理器控制台**" 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="56327-116">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="56327-117">对于 Visual Studio Code，请从**集成终端**运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="56327-117">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="56327-118">npm 将包内容安装到\*node_modules \\ @microsoft\signalr\dist\browser \*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="56327-118">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="56327-119">在*wwwroot \\ lib*文件夹下创建名为*signalr*的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="56327-119">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="56327-120">将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。</span><span class="sxs-lookup"><span data-stu-id="56327-120">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="56327-121">npm 将包内容安装到\*node_modules \\ @aspnet\signalr\dist\browser \*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="56327-121">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="56327-122">在*wwwroot \\ lib*文件夹下创建名为*signalr*的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="56327-122">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="56327-123">将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。</span><span class="sxs-lookup"><span data-stu-id="56327-123">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="56327-124">SignalR在元素中引用 JavaScript 客户端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="56327-124">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="56327-125">例如：</span><span class="sxs-lookup"><span data-stu-id="56327-125">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="56327-126">使用内容交付网络（CDN）</span><span class="sxs-lookup"><span data-stu-id="56327-126">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="56327-127">若要在不使用 npm 先决条件的情况下使用客户端库，请引用 CDN 托管的客户端库副本。</span><span class="sxs-lookup"><span data-stu-id="56327-127">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="56327-128">例如：</span><span class="sxs-lookup"><span data-stu-id="56327-128">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="56327-129">以下 Cdn 提供了客户端库：</span><span class="sxs-lookup"><span data-stu-id="56327-129">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="56327-130">cdnjs</span><span class="sxs-lookup"><span data-stu-id="56327-130">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="56327-131">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="56327-131">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="56327-132">unpkg</span><span class="sxs-lookup"><span data-stu-id="56327-132">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="56327-133">cdnjs</span><span class="sxs-lookup"><span data-stu-id="56327-133">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="56327-134">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="56327-134">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="56327-135">unpkg</span><span class="sxs-lookup"><span data-stu-id="56327-135">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="56327-136">通过 LibMan 安装</span><span class="sxs-lookup"><span data-stu-id="56327-136">Install with LibMan</span></span>

<span data-ttu-id="56327-137">[LibMan](xref:client-side/libman/index)可用于从 CDN 托管的客户端库安装特定的客户端库文件。</span><span class="sxs-lookup"><span data-stu-id="56327-137">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="56327-138">例如，仅将缩小 JavaScript 文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="56327-138">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="56327-139">有关该方法的详细信息，请参阅[添加 SignalR 客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="56327-139">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="56327-140">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="56327-140">Connect to a hub</span></span>

<span data-ttu-id="56327-141">下面的代码创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="56327-141">The following code creates and starts a connection.</span></span> <span data-ttu-id="56327-142">中心名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="56327-142">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="56327-143">跨域连接</span><span class="sxs-lookup"><span data-stu-id="56327-143">Cross-origin connections</span></span>

<span data-ttu-id="56327-144">通常，浏览器从与请求的页相同的域中加载连接。</span><span class="sxs-lookup"><span data-stu-id="56327-144">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="56327-145">但是，在某些情况下，需要与另一个域建立连接。</span><span class="sxs-lookup"><span data-stu-id="56327-145">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="56327-146">为了防止恶意站点读取其他站点中的敏感数据，默认情况下会禁用[跨域连接](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="56327-146">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="56327-147">若要允许跨源请求，请在类中启用它 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="56327-147">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="56327-148">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="56327-148">Call hub methods from client</span></span>

<span data-ttu-id="56327-149">JavaScript 客户端通过[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。</span><span class="sxs-lookup"><span data-stu-id="56327-149">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="56327-150">此 `invoke` 方法接受两个参数：</span><span class="sxs-lookup"><span data-stu-id="56327-150">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="56327-151">集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="56327-151">The name of the hub method.</span></span> <span data-ttu-id="56327-152">在下面的示例中，中心的方法名称是 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="56327-152">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="56327-153">在 hub 方法中定义的所有参数。</span><span class="sxs-lookup"><span data-stu-id="56327-153">Any arguments defined in the hub method.</span></span> <span data-ttu-id="56327-154">在下面的示例中，自变量名称为 `message` 。</span><span class="sxs-lookup"><span data-stu-id="56327-154">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="56327-155">示例代码使用了在所有主要浏览器（Internet Explorer 除外）的当前版本中受支持的箭头函数语法。</span><span class="sxs-lookup"><span data-stu-id="56327-155">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="56327-156">如果 SignalR 在*无服务器模式下*使用 Azure 服务，则无法从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="56327-156">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="56327-157">有关详细信息，请参阅[ SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="56327-157">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="56327-158">`invoke`方法返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="56327-158">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="56327-159">`Promise`当服务器上的方法返回时，将解析为返回值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="56327-159">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="56327-160">如果服务器上的方法引发错误，将拒绝， `Promise` 并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="56327-160">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="56327-161">使用 `then` 和 `catch` 方法 `Promise` 来处理这些情况（或 `await` 语法）。</span><span class="sxs-lookup"><span data-stu-id="56327-161">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="56327-162">`send`方法返回 JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="56327-162">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="56327-163">`Promise`当消息已发送到服务器时，将解决。</span><span class="sxs-lookup"><span data-stu-id="56327-163">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="56327-164">如果发送消息时出错，将 `Promise` 拒绝，并出现错误消息。</span><span class="sxs-lookup"><span data-stu-id="56327-164">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="56327-165">使用 `then` 和 `catch` 方法 `Promise` 来处理这些情况（或 `await` 语法）。</span><span class="sxs-lookup"><span data-stu-id="56327-165">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="56327-166">使用 `send` 不会等到服务器收到消息。</span><span class="sxs-lookup"><span data-stu-id="56327-166">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="56327-167">因此，不可能从服务器返回数据或错误。</span><span class="sxs-lookup"><span data-stu-id="56327-167">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="56327-168">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="56327-168">Call client methods from hub</span></span>

<span data-ttu-id="56327-169">若要从中心接收消息，请使用的[on](/javascript/api/%40aspnet/signalr/hubconnection#on)方法定义方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="56327-169">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="56327-170">JavaScript 客户端方法的名称。</span><span class="sxs-lookup"><span data-stu-id="56327-170">The name of the JavaScript client method.</span></span> <span data-ttu-id="56327-171">在下面的示例中，方法名称是 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="56327-171">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="56327-172">集线器传递给方法的参数。</span><span class="sxs-lookup"><span data-stu-id="56327-172">Arguments the hub passes to the method.</span></span> <span data-ttu-id="56327-173">在下面的示例中，参数值为 `message` 。</span><span class="sxs-lookup"><span data-stu-id="56327-173">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="56327-174">`connection.on`当服务器端代码使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法调用时，上面的代码将运行。</span><span class="sxs-lookup"><span data-stu-id="56327-174">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="56327-175">通过匹配和中定义的方法名称和参数，确定要调用的客户端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="56327-175"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="56327-176">作为最佳做法，请在后面调用[start](/javascript/api/%40aspnet/signalr/hubconnection#start)方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="56327-176">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="56327-177">这样做可确保在收到消息之前注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="56327-177">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="56327-178">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="56327-178">Error handling and logging</span></span>

<span data-ttu-id="56327-179">将 `catch` 方法链接到方法的末尾 `start` ，以处理客户端错误。</span><span class="sxs-lookup"><span data-stu-id="56327-179">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="56327-180">使用 `console.error` 将错误输出到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="56327-180">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="56327-181">设置客户端日志跟踪，方法是在建立连接时将记录器和事件类型传递给日志。</span><span class="sxs-lookup"><span data-stu-id="56327-181">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="56327-182">记录的消息具有指定的日志级别和更高的日志级别。</span><span class="sxs-lookup"><span data-stu-id="56327-182">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="56327-183">可用的日志级别如下所示：</span><span class="sxs-lookup"><span data-stu-id="56327-183">Available log levels are as follows:</span></span>

* <span data-ttu-id="56327-184">`signalR.LogLevel.Error`&ndash;错误消息。</span><span class="sxs-lookup"><span data-stu-id="56327-184">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="56327-185">`Error`仅记录消息。</span><span class="sxs-lookup"><span data-stu-id="56327-185">Logs `Error` messages only.</span></span>
* <span data-ttu-id="56327-186">`signalR.LogLevel.Warning`&ndash;有关潜在错误的警告消息。</span><span class="sxs-lookup"><span data-stu-id="56327-186">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="56327-187">日志 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="56327-187">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="56327-188">`signalR.LogLevel.Information`&ndash;无错误的状态消息。</span><span class="sxs-lookup"><span data-stu-id="56327-188">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="56327-189">日志 `Information` 、 `Warning` 和 `Error` 消息。</span><span class="sxs-lookup"><span data-stu-id="56327-189">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="56327-190">`signalR.LogLevel.Trace`&ndash;跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="56327-190">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="56327-191">记录所有内容，包括中心和客户端之间传输的数据。</span><span class="sxs-lookup"><span data-stu-id="56327-191">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="56327-192">使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="56327-192">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="56327-193">消息将记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="56327-193">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="56327-194">重新连接客户端</span><span class="sxs-lookup"><span data-stu-id="56327-194">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="56327-195">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="56327-195">Automatically reconnect</span></span>

<span data-ttu-id="56327-196">的 JavaScript 客户端 SignalR 可以配置为使用 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56327-196">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="56327-197">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56327-197">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="56327-198">在没有任何参数的情况下，会 `withAutomaticReconnect()` 将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。</span><span class="sxs-lookup"><span data-stu-id="56327-198">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="56327-199">在开始任何重新连接尝试之前， `HubConnection` 将转换为 `HubConnectionState.Reconnecting` 状态，并激发其 `onreconnecting` 回调，而不是转换为 `Disconnected` 状态，并触发其 `onclose` 回调，如 `HubConnection` 不配置自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="56327-199">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="56327-200">这为用户提供警告连接已丢失并禁用 UI 元素的机会。</span><span class="sxs-lookup"><span data-stu-id="56327-200">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56327-201">如果客户端在其前四次尝试内成功重新连接，则 `HubConnection` 将转换回 `Connected` 状态并激发其 `onreconnected` 回调。</span><span class="sxs-lookup"><span data-stu-id="56327-201">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="56327-202">这为用户提供了通知用户连接已重新建立的机会。</span><span class="sxs-lookup"><span data-stu-id="56327-202">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="56327-203">由于连接在服务器上看起来是全新的，因此 `connectionId` 将向回调提供一个新的 `onreconnected` 。</span><span class="sxs-lookup"><span data-stu-id="56327-203">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="56327-204">`onreconnected` `connectionId` 如果 `HubConnection` 配置为[跳过协商](xref:signalr/configuration#configure-client-options)，则不会定义回调的参数。</span><span class="sxs-lookup"><span data-stu-id="56327-204">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56327-205">`withAutomaticReconnect()`不会将配置 `HubConnection` 为重试初始启动失败，因此，需要手动处理启动失败：</span><span class="sxs-lookup"><span data-stu-id="56327-205">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="56327-206">如果客户端在其前四次尝试中未成功重新连接，则 `HubConnection` 将转换为 `Disconnected` 状态并激发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。</span><span class="sxs-lookup"><span data-stu-id="56327-206">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="56327-207">这为用户提供了通知用户连接永久丢失的机会，并建议刷新页面：</span><span class="sxs-lookup"><span data-stu-id="56327-207">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="56327-208">若要在断开连接或更改重新连接时间安排之前配置自定义的重新连接尝试次数，请 `withAutomaticReconnect` 接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="56327-208">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="56327-209">前面的示例将配置 `HubConnection` 为在连接丢失后立即开始尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="56327-209">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="56327-210">这也适用于默认配置。</span><span class="sxs-lookup"><span data-stu-id="56327-210">This is also true for the default configuration.</span></span>

<span data-ttu-id="56327-211">如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="56327-211">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="56327-212">如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。</span><span class="sxs-lookup"><span data-stu-id="56327-212">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="56327-213">然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离，而不是在另一个30秒内尝试再次尝试重新连接，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="56327-213">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="56327-214">如果需要更好地控制计时和自动重新连接尝试的次数，则 `withAutomaticReconnect` 接受一个实现接口的 `IRetryPolicy` 对象，该对象具有一个名为的方法 `nextRetryDelayInMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="56327-214">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="56327-215">`nextRetryDelayInMilliseconds`采用类型为的单个自变量 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="56327-215">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="56327-216">`RetryContext`具有三个属性： `previousRetryCount` `elapsedMilliseconds` 和分别为 `retryReason` `number` 、 `number` 和 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="56327-216">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="56327-217">第一次重新连接尝试之前， `previousRetryCount` 和都 `elapsedMilliseconds` 是零， `retryReason` 将是导致连接丢失的错误。</span><span class="sxs-lookup"><span data-stu-id="56327-217">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="56327-218">每次失败的重试次数递增一次后，将进行 `previousRetryCount` `elapsedMilliseconds` 更新，以反映到目前为止的重新连接所用的时间（以毫秒为单位），并且 `retryReason` 将是导致上次重新连接尝试失败的错误。</span><span class="sxs-lookup"><span data-stu-id="56327-218">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="56327-219">`nextRetryDelayInMilliseconds`必须返回一个数字，该数字表示在下一次重新连接尝试之前要等待的毫秒数，或者 `null` ，如果应停止重新连接，则为 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="56327-219">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="56327-220">或者，你可以编写将手动重新连接客户端的代码，如[手动重新连接](#manually-reconnect)中所示。</span><span class="sxs-lookup"><span data-stu-id="56327-220">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="56327-221">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="56327-221">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="56327-222">在3.0 之前， SignalR 不会自动重新连接 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="56327-222">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="56327-223">必须编写代码来手动重新连接客户端。</span><span class="sxs-lookup"><span data-stu-id="56327-223">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="56327-224">下面的代码演示典型的手动重新连接方法：</span><span class="sxs-lookup"><span data-stu-id="56327-224">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="56327-225">创建函数（在本例中为 `start` 函数）以启动连接。</span><span class="sxs-lookup"><span data-stu-id="56327-225">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="56327-226">`start`在连接的 `onclose` 事件处理程序中调用函数。</span><span class="sxs-lookup"><span data-stu-id="56327-226">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="56327-227">实际的实现将使用指数回退或在放弃之前重试指定的次数。</span><span class="sxs-lookup"><span data-stu-id="56327-227">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56327-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="56327-228">Additional resources</span></span>

* [<span data-ttu-id="56327-229">JavaScript API 参考</span><span class="sxs-lookup"><span data-stu-id="56327-229">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="56327-230">JavaScript 教程</span><span class="sxs-lookup"><span data-stu-id="56327-230">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="56327-231">WebPack 和 TypeScript 教程</span><span class="sxs-lookup"><span data-stu-id="56327-231">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="56327-232">中心</span><span class="sxs-lookup"><span data-stu-id="56327-232">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="56327-233">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="56327-233">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="56327-234">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="56327-234">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="56327-235">跨域请求（CORS）</span><span class="sxs-lookup"><span data-stu-id="56327-235">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="56327-236">[Azure SignalR Service 无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="56327-236">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
