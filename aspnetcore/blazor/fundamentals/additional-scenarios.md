---
title: ASP.NET Core Blazor 托管模型配置
author: guardrex
description: 了解有关 ASP.NET Core Blazor 托管模型配置的其他方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818997"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="1fc82-103">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="1fc82-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="1fc82-104">作者：[Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1fc82-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1fc82-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="1fc82-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="1fc82-106">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="1fc82-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="1fc82-107">本部分适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="1fc82-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="1fc82-108">若要将 SignalR 的基础客户端配置为发送凭据（如 Cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1fc82-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="1fc82-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 提取请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：</span><span class="sxs-lookup"><span data-stu-id="1fc82-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="1fc82-110">将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="1fc82-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="1fc82-111">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="1fc82-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="1fc82-112">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="1fc82-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="1fc82-113">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="1fc82-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1fc82-114">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="1fc82-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="1fc82-115">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="1fc82-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="1fc82-116">若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="1fc82-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="1fc82-117">向应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="1fc82-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="1fc82-118">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="1fc82-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="1fc82-119">CSS 类</span><span class="sxs-lookup"><span data-stu-id="1fc82-119">CSS class</span></span>                       | <span data-ttu-id="1fc82-120">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="1fc82-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="1fc82-121">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="1fc82-121">A lost connection.</span></span> <span data-ttu-id="1fc82-122">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="1fc82-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="1fc82-123">显示模式。</span><span class="sxs-lookup"><span data-stu-id="1fc82-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="1fc82-124">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="1fc82-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="1fc82-125">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="1fc82-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="1fc82-126">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="1fc82-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="1fc82-127">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="1fc82-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="1fc82-128">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="1fc82-128">Reconnection rejected.</span></span> <span data-ttu-id="1fc82-129">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="1fc82-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="1fc82-130">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="1fc82-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="1fc82-131">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="1fc82-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="1fc82-132">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="1fc82-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="1fc82-133">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="1fc82-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="1fc82-134">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="1fc82-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="1fc82-135">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="1fc82-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="1fc82-136">呈现模式</span><span class="sxs-lookup"><span data-stu-id="1fc82-136">Render mode</span></span>

<span data-ttu-id="1fc82-137">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="1fc82-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1fc82-138">默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="1fc82-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="1fc82-139">这是在 `_Host.cshtml` Razor 页中设置的：</span><span class="sxs-lookup"><span data-stu-id="1fc82-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="1fc82-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="1fc82-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1fc82-141">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="1fc82-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="1fc82-142">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1fc82-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="1fc82-143">呈现模式</span><span class="sxs-lookup"><span data-stu-id="1fc82-143">Render mode</span></span> | <span data-ttu-id="1fc82-144">描述</span><span class="sxs-lookup"><span data-stu-id="1fc82-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="1fc82-145">在静态 HTML 中呈现组件，并包含 Blazor Server 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1fc82-146">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1fc82-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="1fc82-147">呈现 Blazor Server 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1fc82-148">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="1fc82-148">Output from the component isn't included.</span></span> <span data-ttu-id="1fc82-149">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1fc82-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="1fc82-150">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="1fc82-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1fc82-151">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="1fc82-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="1fc82-152">为 Blazor Server 应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="1fc82-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="1fc82-153">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="1fc82-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="1fc82-154">在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="1fc82-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="1fc82-155">将调用 `Blazor.start` 的脚本放置在 `_framework/blazor.server.js` 脚本之后的 `</body>` 标记内。</span><span class="sxs-lookup"><span data-stu-id="1fc82-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="1fc82-156">Logging</span><span class="sxs-lookup"><span data-stu-id="1fc82-156">Logging</span></span>

<span data-ttu-id="1fc82-157">若要配置 SignalR 客户端日志记录，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1fc82-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="1fc82-158">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="1fc82-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1fc82-159">传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别。</span><span class="sxs-lookup"><span data-stu-id="1fc82-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
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

<span data-ttu-id="1fc82-160">在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="1fc82-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="1fc82-161">修改重新连接处理程序</span><span class="sxs-lookup"><span data-stu-id="1fc82-161">Modify the reconnection handler</span></span>

<span data-ttu-id="1fc82-162">可以针对自定义行为修改重新连接处理程序的线路连接事件，如：</span><span class="sxs-lookup"><span data-stu-id="1fc82-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="1fc82-163">在连接断开时通知用户。</span><span class="sxs-lookup"><span data-stu-id="1fc82-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="1fc82-164">在线路连接时（通过客户端）执行日志记录。</span><span class="sxs-lookup"><span data-stu-id="1fc82-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="1fc82-165">若要修改连接事件，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1fc82-165">To modify the connection events:</span></span>

* <span data-ttu-id="1fc82-166">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="1fc82-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1fc82-167">为断开的连接 (`onConnectionDown`) 和建立/重新建立的连接 (`onConnectionUp`) 注册连接更改回叫。</span><span class="sxs-lookup"><span data-stu-id="1fc82-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="1fc82-168">必须同时指定 `onConnectionDown` 和 `onConnectionUp`。</span><span class="sxs-lookup"><span data-stu-id="1fc82-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="1fc82-169">调整重新连接重试计数和间隔</span><span class="sxs-lookup"><span data-stu-id="1fc82-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="1fc82-170">若要调整重新连接重试计数和间隔，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1fc82-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="1fc82-171">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="1fc82-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1fc82-172">设置允许的每次重试尝试 (`retryIntervalMilliseconds`) 的重试次数 (`maxRetries`) 和时间间隔（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="1fc82-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="1fc82-173">隐藏或替换重新连接显示</span><span class="sxs-lookup"><span data-stu-id="1fc82-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="1fc82-174">若要隐藏重新连接显示，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1fc82-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="1fc82-175">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="1fc82-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1fc82-176">将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）。</span><span class="sxs-lookup"><span data-stu-id="1fc82-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="1fc82-177">若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：</span><span class="sxs-lookup"><span data-stu-id="1fc82-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="1fc82-178">占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。</span><span class="sxs-lookup"><span data-stu-id="1fc82-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="1fc82-179">影响 HTML `<head>` 标记元素</span><span class="sxs-lookup"><span data-stu-id="1fc82-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="1fc82-180">本部分适用于 Blazor WebAssembly 和 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="1fc82-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="1fc82-181">`Title`、`Link` 和 `Meta` 组件呈现时，会在 HTML `<head>` 标记元素中添加或更新数据：</span><span class="sxs-lookup"><span data-stu-id="1fc82-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="1fc82-182">在前面的示例中，`{TITLE}`、`{URL}` 和 `{DESCRIPTION}` 的占位符是字符串值、Razor 变量或 Razor 表达式。</span><span class="sxs-lookup"><span data-stu-id="1fc82-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="1fc82-183">具有以下特性：</span><span class="sxs-lookup"><span data-stu-id="1fc82-183">The following characteristics apply:</span></span>

* <span data-ttu-id="1fc82-184">支持服务器端预呈现。</span><span class="sxs-lookup"><span data-stu-id="1fc82-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="1fc82-185">`Value` 参数是 `Title` 组件唯一有效的参数。</span><span class="sxs-lookup"><span data-stu-id="1fc82-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="1fc82-186">为 `Meta` 和 `Link` 组件提供的 HTML 属性是在[其他属性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)中捕获的，并传递到呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="1fc82-187">对于多个 `Title` 组件，页的标题反映了呈现的最后一个 `Title` 组件的 `Value`。</span><span class="sxs-lookup"><span data-stu-id="1fc82-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="1fc82-188">如果多个 `Meta` 或 `Link` 组件包含在相同的属性中，则每个 `Meta` 或 `Link` 组件呈现且仅呈现一个 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="1fc82-189">两个 `Meta` 或 `Link` 组件不能引用同一个呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="1fc82-190">对现有 `Meta` 或 `Link` 组件的参数所做的更改将在其呈现的 HTML 标记中体现。</span><span class="sxs-lookup"><span data-stu-id="1fc82-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="1fc82-191">如果不再呈现 `Link` 或 `Meta` 组件，并因此由框架释放，则会删除其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="1fc82-192">在子组件中使用其中一个框架组件时，只要呈现包含框架组件的子组件，呈现的 HTML 标记就会影响父组件的任何其他子组件。</span><span class="sxs-lookup"><span data-stu-id="1fc82-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="1fc82-193">在子组件中使用其中一个框架组件，与在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 中放置一个 HTML 标记之间的区别是，框架组件已呈现的 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="1fc82-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="1fc82-194">可以根据应用程序状态进行修改。</span><span class="sxs-lookup"><span data-stu-id="1fc82-194">Can be modified by application state.</span></span> <span data-ttu-id="1fc82-195">不能根据应用程序状态修改硬编码 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1fc82-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="1fc82-196">将在不再呈现父组件的情况下从 HTML `<head>` 中被删除。</span><span class="sxs-lookup"><span data-stu-id="1fc82-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1fc82-197">其他资源</span><span class="sxs-lookup"><span data-stu-id="1fc82-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
