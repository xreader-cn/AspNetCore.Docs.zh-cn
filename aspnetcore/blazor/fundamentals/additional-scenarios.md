---
title: ASP.NET Core Blazor 托管模型配置
author: guardrex
description: 了解有关 ASP.NET Core Blazor 托管模型配置的其他方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485870"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="3a102-103">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="3a102-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3a102-104">作者：[Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a102-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3a102-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="3a102-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="3a102-106">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="3a102-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="3a102-107">本部分适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="3a102-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="3a102-108">若要将 SignalR 的基础客户端配置为发送凭据（如 cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3a102-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="3a102-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 提取请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：</span><span class="sxs-lookup"><span data-stu-id="3a102-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="3a102-110">将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="3a102-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="3a102-111">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="3a102-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3a102-112">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="3a102-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3a102-113">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="3a102-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="3a102-114">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="3a102-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3a102-115">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="3a102-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3a102-116">若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="3a102-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3a102-117">向应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="3a102-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="3a102-118">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="3a102-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3a102-119">CSS 类</span><span class="sxs-lookup"><span data-stu-id="3a102-119">CSS class</span></span>                       | <span data-ttu-id="3a102-120">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="3a102-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="3a102-121">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="3a102-121">A lost connection.</span></span> <span data-ttu-id="3a102-122">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="3a102-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="3a102-123">显示模式。</span><span class="sxs-lookup"><span data-stu-id="3a102-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="3a102-124">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="3a102-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="3a102-125">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="3a102-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="3a102-126">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="3a102-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3a102-127">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="3a102-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="3a102-128">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="3a102-128">Reconnection rejected.</span></span> <span data-ttu-id="3a102-129">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="3a102-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3a102-130">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="3a102-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3a102-131">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="3a102-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="3a102-132">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="3a102-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3a102-133">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="3a102-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3a102-134">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="3a102-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3a102-135">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="3a102-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="3a102-136">呈现模式</span><span class="sxs-lookup"><span data-stu-id="3a102-136">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3a102-137">*此部分适用于托管的 Blazor WebAssembly 和 Blazor Server。*</span><span class="sxs-lookup"><span data-stu-id="3a102-137">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="3a102-138">Blazor 默认设置为在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="3a102-138">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="3a102-139">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="3a102-139">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3a102-140">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="3a102-140">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="3a102-141">默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="3a102-141">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3a102-142">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="3a102-142">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="3a102-143">初始化 Blazor 回路</span><span class="sxs-lookup"><span data-stu-id="3a102-143">Initialize the Blazor circuit</span></span>

<span data-ttu-id="3a102-144">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="3a102-144">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="3a102-145">在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用 [SignalR 回路 ](xref:blazor/hosting-models#circuits) 的手动启动：</span><span class="sxs-lookup"><span data-stu-id="3a102-145">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="3a102-146">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="3a102-146">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3a102-147">将调用 `Blazor.start` 的脚本放置在 `blazor.server.js` 脚本标记之后并放在结束的 `</body>` 标记内。</span><span class="sxs-lookup"><span data-stu-id="3a102-147">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="3a102-148">禁用 `autostart` 时，应用中不依赖该回路的任何方面都能正常工作。</span><span class="sxs-lookup"><span data-stu-id="3a102-148">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="3a102-149">例如，客户端路由正常运行。</span><span class="sxs-lookup"><span data-stu-id="3a102-149">For example, client-side routing is operational.</span></span> <span data-ttu-id="3a102-150">但是，在调用 `Blazor.start` 之前，依赖于该回路的任何方面不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="3a102-150">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="3a102-151">如果没有已建立的回路，应用行为是不可预测的。</span><span class="sxs-lookup"><span data-stu-id="3a102-151">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="3a102-152">例如，在回路断开连接时，组件方法无法执行。</span><span class="sxs-lookup"><span data-stu-id="3a102-152">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="3a102-153">文档准备就绪时初始化 Blazor</span><span class="sxs-lookup"><span data-stu-id="3a102-153">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="3a102-154">文档准备就绪时初始化 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="3a102-154">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="3a102-155">链接到由手动启动生成的 `Promise`</span><span class="sxs-lookup"><span data-stu-id="3a102-155">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="3a102-156">若要执行其他任务（如 JS 互操作初始化），请使用 `then` 链接到 `Promise`（由手动 Blazor 应用启动生成）：</span><span class="sxs-lookup"><span data-stu-id="3a102-156">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="3a102-157">配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="3a102-157">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="3a102-158">日志记录</span><span class="sxs-lookup"><span data-stu-id="3a102-158">Logging</span></span>

<span data-ttu-id="3a102-159">若要配置 SignalR 客户端日志，请传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别：</span><span class="sxs-lookup"><span data-stu-id="3a102-159">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="3a102-160">在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3a102-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="3a102-161">修改重新连接处理程序</span><span class="sxs-lookup"><span data-stu-id="3a102-161">Modify the reconnection handler</span></span>

<span data-ttu-id="3a102-162">可以针对自定义行为修改重新连接处理程序的线路连接事件，如：</span><span class="sxs-lookup"><span data-stu-id="3a102-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="3a102-163">在连接断开时通知用户。</span><span class="sxs-lookup"><span data-stu-id="3a102-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="3a102-164">在线路连接时（通过客户端）执行日志记录。</span><span class="sxs-lookup"><span data-stu-id="3a102-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="3a102-165">若要修改连接事件，请为以下连接更改注册回调：</span><span class="sxs-lookup"><span data-stu-id="3a102-165">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="3a102-166">使用 `onConnectionDown` 删除的连接。</span><span class="sxs-lookup"><span data-stu-id="3a102-166">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="3a102-167">已建立/重新建立的连接使用 `onConnectionUp`。</span><span class="sxs-lookup"><span data-stu-id="3a102-167">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="3a102-168">必须同时指定 `onConnectionDown` 和 `onConnectionUp`：</span><span class="sxs-lookup"><span data-stu-id="3a102-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="3a102-169">调整重新连接重试计数和间隔</span><span class="sxs-lookup"><span data-stu-id="3a102-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="3a102-170">若要调整重新连接重试次数和间隔，请设置重试次数 (`maxRetries`) 和允许每次重试运行的毫秒数 (`retryIntervalMilliseconds`)：</span><span class="sxs-lookup"><span data-stu-id="3a102-170">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="3a102-171">隐藏或替换重新连接显示</span><span class="sxs-lookup"><span data-stu-id="3a102-171">Hide or replace the reconnection display</span></span>

<span data-ttu-id="3a102-172">若要隐藏重新连接显示，请将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）：</span><span class="sxs-lookup"><span data-stu-id="3a102-172">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="3a102-173">若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：</span><span class="sxs-lookup"><span data-stu-id="3a102-173">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="3a102-174">占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。</span><span class="sxs-lookup"><span data-stu-id="3a102-174">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3a102-175">通过在应用的 CSS (`wwwroot/css/site.css`) 中为模式元素设置 `transition-delay` 属性，自定义重新连接显示出现之前的延迟。</span><span class="sxs-lookup"><span data-stu-id="3a102-175">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="3a102-176">以下示例将转换延迟从 500 毫秒（默认值）设置为 1000 毫秒（1 秒）：</span><span class="sxs-lookup"><span data-stu-id="3a102-176">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="3a102-177">从客户端断开 Blazor 线路连接</span><span class="sxs-lookup"><span data-stu-id="3a102-177">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="3a102-178">默认情况下，触发 [`unload` 页面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)时，Blazor 线路会断开连接。</span><span class="sxs-lookup"><span data-stu-id="3a102-178">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="3a102-179">若要断开客户端上其他方案的线路连接，请在相应的事件处理程序中调用 `Blazor.disconnect`。</span><span class="sxs-lookup"><span data-stu-id="3a102-179">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="3a102-180">在下面的示例中，当页面隐藏（[`pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)）时，线路会断开连接：</span><span class="sxs-lookup"><span data-stu-id="3a102-180">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="3a102-181">静态文件</span><span class="sxs-lookup"><span data-stu-id="3a102-181">Static files</span></span>

<span data-ttu-id="3a102-182">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="3a102-182">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="3a102-183">若要使用 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 创建其他文件映射，或者要配置其他 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>，请使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="3a102-183">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="3a102-184">在以下示例中，`{EXTENSION}` 占位符为文件扩展名，`{CONTENT TYPE}` 占位符为内容类型。</span><span class="sxs-lookup"><span data-stu-id="3a102-184">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="3a102-185">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 通过 `Startup.ConfigureServices` (`Startup.cs`) 中的[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 来配置选项：</span><span class="sxs-lookup"><span data-stu-id="3a102-185">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="3a102-186">此方法会配置用于为 `blazor.server.js`提供服务的相同文件提供程序，因此请确保你的自定义配置不会干扰为 `blazor.server.js` 提供服务。</span><span class="sxs-lookup"><span data-stu-id="3a102-186">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="3a102-187">例如，不要通过使用 `provider.Mappings.Remove(".js")` 配置提供程序来删除 JavaScript 文件的映射。</span><span class="sxs-lookup"><span data-stu-id="3a102-187">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="3a102-188">在 `Startup.Configure` (`Startup.cs`) 中使用两次对 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 的调用：</span><span class="sxs-lookup"><span data-stu-id="3a102-188">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="3a102-189">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 在第一次调用中配置自定义文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="3a102-189">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="3a102-190">第二个中间件提供 `blazor.server.js`，其使用 Blazor 框架提供的默认静态文件配置。</span><span class="sxs-lookup"><span data-stu-id="3a102-190">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="3a102-191">可以使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 执行自定义静态文件中间件来避免在提供 `_framework/blazor.server.js` 时受到干扰：</span><span class="sxs-lookup"><span data-stu-id="3a102-191">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="3a102-192">其他资源</span><span class="sxs-lookup"><span data-stu-id="3a102-192">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* [<span data-ttu-id="3a102-193">Blazor Server 重新连接事件和组件生命周期事件</span><span class="sxs-lookup"><span data-stu-id="3a102-193">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
