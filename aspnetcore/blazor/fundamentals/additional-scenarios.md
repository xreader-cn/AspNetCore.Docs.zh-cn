---
title: ASP.NET Core Blazor 托管模型配置
author: guardrex
description: 了解有关 ASP.NET Core Blazor 托管模型配置的其他方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
ms.openlocfilehash: 075bcc68fd2dff0ebf2cfceacec24fde8c818603
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326542"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="5349c-103">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="5349c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="5349c-104">作者：[Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5349c-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5349c-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="5349c-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="5349c-106">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="5349c-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="5349c-107">本部分适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="5349c-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="5349c-108">若要将 SignalR 的基础客户端配置为发送凭据（如 cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5349c-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="5349c-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 提取请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：</span><span class="sxs-lookup"><span data-stu-id="5349c-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="5349c-110">将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="5349c-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="5349c-111">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="5349c-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="5349c-112">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="5349c-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="5349c-113">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="5349c-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5349c-114">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="5349c-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="5349c-115">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="5349c-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="5349c-116">若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="5349c-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="5349c-117">向应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="5349c-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="5349c-118">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="5349c-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="5349c-119">CSS 类</span><span class="sxs-lookup"><span data-stu-id="5349c-119">CSS class</span></span>                       | <span data-ttu-id="5349c-120">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="5349c-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="5349c-121">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="5349c-121">A lost connection.</span></span> <span data-ttu-id="5349c-122">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="5349c-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="5349c-123">显示模式。</span><span class="sxs-lookup"><span data-stu-id="5349c-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="5349c-124">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="5349c-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="5349c-125">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="5349c-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="5349c-126">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="5349c-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="5349c-127">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="5349c-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="5349c-128">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="5349c-128">Reconnection rejected.</span></span> <span data-ttu-id="5349c-129">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="5349c-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="5349c-130">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="5349c-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="5349c-131">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="5349c-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="5349c-132">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="5349c-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="5349c-133">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="5349c-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="5349c-134">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="5349c-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="5349c-135">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="5349c-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="5349c-136">呈现模式</span><span class="sxs-lookup"><span data-stu-id="5349c-136">Render mode</span></span>

<span data-ttu-id="5349c-137">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="5349c-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5349c-138">默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="5349c-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="5349c-139">这是在 `_Host.cshtml` Razor 页中设置的：</span><span class="sxs-lookup"><span data-stu-id="5349c-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="5349c-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="5349c-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="5349c-141">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="5349c-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="5349c-142">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="5349c-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="5349c-143">呈现模式</span><span class="sxs-lookup"><span data-stu-id="5349c-143">Render mode</span></span> | <span data-ttu-id="5349c-144">描述</span><span class="sxs-lookup"><span data-stu-id="5349c-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5349c-145">在静态 HTML 中呈现组件，并包含 Blazor Server 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5349c-146">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5349c-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5349c-147">呈现 Blazor Server 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5349c-148">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="5349c-148">Output from the component isn't included.</span></span> <span data-ttu-id="5349c-149">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5349c-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5349c-150">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="5349c-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="5349c-151">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="5349c-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="5349c-152">初始化 Blazor 回路</span><span class="sxs-lookup"><span data-stu-id="5349c-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="5349c-153">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="5349c-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5349c-154">在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用 [SignalR 回路 ](xref:blazor/hosting-models#circuits) 的手动启动：</span><span class="sxs-lookup"><span data-stu-id="5349c-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="5349c-155">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="5349c-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="5349c-156">将调用 `Blazor.start` 的脚本放置在 `blazor.server.js` 脚本标记之后并放在结束的 `</body>` 标记内。</span><span class="sxs-lookup"><span data-stu-id="5349c-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="5349c-157">禁用 `autostart` 时，应用中不依赖该回路的任何方面都能正常工作。</span><span class="sxs-lookup"><span data-stu-id="5349c-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="5349c-158">例如，客户端路由正常运行。</span><span class="sxs-lookup"><span data-stu-id="5349c-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="5349c-159">但是，在调用 `Blazor.start` 之前，依赖于该回路的任何方面不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="5349c-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="5349c-160">如果没有已建立的回路，应用行为是不可预测的。</span><span class="sxs-lookup"><span data-stu-id="5349c-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="5349c-161">例如，在回路断开连接时，组件方法无法执行。</span><span class="sxs-lookup"><span data-stu-id="5349c-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="5349c-162">文档准备就绪时初始化 Blazor</span><span class="sxs-lookup"><span data-stu-id="5349c-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="5349c-163">文档准备就绪时初始化 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="5349c-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="5349c-164">链接到由手动启动生成的 `Promise`</span><span class="sxs-lookup"><span data-stu-id="5349c-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="5349c-165">若要执行其他任务（如 JS 互操作初始化），请使用 `then` 链接到 `Promise`（由手动 Blazor 应用启动生成）：</span><span class="sxs-lookup"><span data-stu-id="5349c-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="5349c-166">配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="5349c-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="5349c-167">日志记录</span><span class="sxs-lookup"><span data-stu-id="5349c-167">Logging</span></span>

<span data-ttu-id="5349c-168">若要配置 SignalR 客户端日志，请传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别：</span><span class="sxs-lookup"><span data-stu-id="5349c-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="5349c-169">在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="5349c-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="5349c-170">修改重新连接处理程序</span><span class="sxs-lookup"><span data-stu-id="5349c-170">Modify the reconnection handler</span></span>

<span data-ttu-id="5349c-171">可以针对自定义行为修改重新连接处理程序的线路连接事件，如：</span><span class="sxs-lookup"><span data-stu-id="5349c-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="5349c-172">在连接断开时通知用户。</span><span class="sxs-lookup"><span data-stu-id="5349c-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="5349c-173">在线路连接时（通过客户端）执行日志记录。</span><span class="sxs-lookup"><span data-stu-id="5349c-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="5349c-174">若要修改连接事件，请为以下连接更改注册回调：</span><span class="sxs-lookup"><span data-stu-id="5349c-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="5349c-175">使用 `onConnectionDown` 删除的连接。</span><span class="sxs-lookup"><span data-stu-id="5349c-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="5349c-176">已建立/重新建立的连接使用 `onConnectionUp`。</span><span class="sxs-lookup"><span data-stu-id="5349c-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="5349c-177">必须同时指定 `onConnectionDown` 和 `onConnectionUp`：</span><span class="sxs-lookup"><span data-stu-id="5349c-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="5349c-178">调整重新连接重试计数和间隔</span><span class="sxs-lookup"><span data-stu-id="5349c-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="5349c-179">若要调整重新连接重试次数和间隔，请设置重试次数 (`maxRetries`) 和允许每次重试运行的毫秒数 (`retryIntervalMilliseconds`)：</span><span class="sxs-lookup"><span data-stu-id="5349c-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="5349c-180">隐藏或替换重新连接显示</span><span class="sxs-lookup"><span data-stu-id="5349c-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="5349c-181">若要隐藏重新连接显示，请将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）：</span><span class="sxs-lookup"><span data-stu-id="5349c-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="5349c-182">若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：</span><span class="sxs-lookup"><span data-stu-id="5349c-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="5349c-183">占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。</span><span class="sxs-lookup"><span data-stu-id="5349c-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5349c-184">通过在应用的 CSS (`wwwroot/css/site.css`) 中为模式元素设置 `transition-delay` 属性，自定义重新连接显示出现之前的延迟。</span><span class="sxs-lookup"><span data-stu-id="5349c-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="5349c-185">以下示例将转换延迟从 500 毫秒（默认值）设置为 1000 毫秒（1 秒）：</span><span class="sxs-lookup"><span data-stu-id="5349c-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="5349c-186">从客户端断开 Blazor 线路连接</span><span class="sxs-lookup"><span data-stu-id="5349c-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="5349c-187">默认情况下，触发 [`unload` 页面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)时，Blazor 线路会断开连接。</span><span class="sxs-lookup"><span data-stu-id="5349c-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="5349c-188">若要断开客户端上其他方案的线路连接，请在相应的事件处理程序中调用 `Blazor.disconnect`。</span><span class="sxs-lookup"><span data-stu-id="5349c-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="5349c-189">在下面的示例中，当页面隐藏（[`pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)）时，线路会断开连接：</span><span class="sxs-lookup"><span data-stu-id="5349c-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="5349c-190">影响 HTML `<head>` 标记元素</span><span class="sxs-lookup"><span data-stu-id="5349c-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="5349c-191">本部分适用于 Blazor WebAssembly 和 Blazor Server 即将发布的 ASP.NET Core 5.0 版本。</span><span class="sxs-lookup"><span data-stu-id="5349c-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="5349c-192">`Title`、`Link` 和 `Meta` 组件呈现时，会在 HTML `<head>` 标记元素中添加或更新数据：</span><span class="sxs-lookup"><span data-stu-id="5349c-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="5349c-193">在前面的示例中，`{TITLE}`、`{URL}` 和 `{DESCRIPTION}` 的占位符是字符串值、Razor 变量或 Razor 表达式。</span><span class="sxs-lookup"><span data-stu-id="5349c-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="5349c-194">具有以下特性：</span><span class="sxs-lookup"><span data-stu-id="5349c-194">The following characteristics apply:</span></span>

* <span data-ttu-id="5349c-195">支持服务器端预呈现。</span><span class="sxs-lookup"><span data-stu-id="5349c-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="5349c-196">`Value` 参数是 `Title` 组件唯一有效的参数。</span><span class="sxs-lookup"><span data-stu-id="5349c-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="5349c-197">为 `Meta` 和 `Link` 组件提供的 HTML 属性是在[其他属性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)中捕获的，并传递到呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="5349c-198">对于多个 `Title` 组件，页的标题反映了呈现的最后一个 `Title` 组件的 `Value`。</span><span class="sxs-lookup"><span data-stu-id="5349c-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="5349c-199">如果多个 `Meta` 或 `Link` 组件包含在相同的属性中，则每个 `Meta` 或 `Link` 组件呈现且仅呈现一个 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="5349c-200">两个 `Meta` 或 `Link` 组件不能引用同一个呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="5349c-201">对现有 `Meta` 或 `Link` 组件的参数所做的更改将在其呈现的 HTML 标记中体现。</span><span class="sxs-lookup"><span data-stu-id="5349c-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="5349c-202">如果不再呈现 `Link` 或 `Meta` 组件，并因此由框架释放，则会删除其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="5349c-203">在子组件中使用其中一个框架组件时，只要呈现包含框架组件的子组件，呈现的 HTML 标记就会影响父组件的任何其他子组件。</span><span class="sxs-lookup"><span data-stu-id="5349c-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="5349c-204">在子组件中使用其中一个框架组件，与在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 中放置一个 HTML 标记之间的区别是，框架组件已呈现的 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="5349c-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="5349c-205">可以根据应用程序状态进行修改。</span><span class="sxs-lookup"><span data-stu-id="5349c-205">Can be modified by application state.</span></span> <span data-ttu-id="5349c-206">不能根据应用程序状态修改硬编码 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5349c-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="5349c-207">将在不再呈现父组件的情况下从 HTML `<head>` 中被删除。</span><span class="sxs-lookup"><span data-stu-id="5349c-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="5349c-208">静态文件</span><span class="sxs-lookup"><span data-stu-id="5349c-208">Static files</span></span>

<span data-ttu-id="5349c-209">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="5349c-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5349c-210">若要使用 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 创建其他文件映射，或者要配置其他 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>，请使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="5349c-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="5349c-211">在以下示例中，`{EXTENSION}` 占位符为文件扩展名，`{CONTENT TYPE}` 占位符为内容类型。</span><span class="sxs-lookup"><span data-stu-id="5349c-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="5349c-212">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 通过 `Startup.ConfigureServices` (`Startup.cs`) 中的[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 来配置选项：</span><span class="sxs-lookup"><span data-stu-id="5349c-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="5349c-213">此方法会配置用于为 `blazor.server.js`提供服务的相同文件提供程序，因此请确保你的自定义配置不会干扰为 `blazor.server.js` 提供服务。</span><span class="sxs-lookup"><span data-stu-id="5349c-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="5349c-214">例如，不要通过使用 `provider.Mappings.Remove(".js")` 配置提供程序来删除 JavaScript 文件的映射。</span><span class="sxs-lookup"><span data-stu-id="5349c-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="5349c-215">在 `Startup.Configure` (`Startup.cs`) 中使用两次对 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 的调用：</span><span class="sxs-lookup"><span data-stu-id="5349c-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="5349c-216">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 在第一次调用中配置自定义文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="5349c-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="5349c-217">第二个中间件提供 `blazor.server.js`，其使用 Blazor 框架提供的默认静态文件配置。</span><span class="sxs-lookup"><span data-stu-id="5349c-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="5349c-218">可以使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 执行自定义静态文件中间件来避免在提供 `_framework/blazor.server.js` 时受到干扰：</span><span class="sxs-lookup"><span data-stu-id="5349c-218">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="5349c-219">其他资源</span><span class="sxs-lookup"><span data-stu-id="5349c-219">Additional resources</span></span>

* <xref:fundamentals/logging/index>
