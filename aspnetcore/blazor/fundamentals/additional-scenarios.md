---
title: ASP.NET Core Blazor 托管模型配置
author: guardrex
description: 了解有关 ASP.NET Core Blazor 托管模型配置的其他方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 726aafd2bf5d3469c30ebce1e4eea8ed8ec8d58e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103195"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="5351c-103">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="5351c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="5351c-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5351c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5351c-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="5351c-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="5351c-106">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="5351c-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="5351c-107">本部分适用于 Blazor WebAssembly。\*\*</span><span class="sxs-lookup"><span data-stu-id="5351c-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="5351c-108">若要将 SignalR 的基础客户端配置为发送凭据（如 Cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5351c-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="5351c-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源[提取](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：</span><span class="sxs-lookup"><span data-stu-id="5351c-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="5351c-110">将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="5351c-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="5351c-111">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="5351c-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="5351c-112">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="5351c-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="5351c-113">本部分适用于 Blazor 服务器。\*\*</span><span class="sxs-lookup"><span data-stu-id="5351c-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5351c-114">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="5351c-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="5351c-115">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="5351c-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="5351c-116">若要自定义 UI，请在 _Host.cshtml\*\* Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="5351c-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="5351c-117">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="5351c-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="5351c-118">CSS 类</span><span class="sxs-lookup"><span data-stu-id="5351c-118">CSS class</span></span>                       | <span data-ttu-id="5351c-119">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="5351c-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="5351c-120">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="5351c-120">A lost connection.</span></span> <span data-ttu-id="5351c-121">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="5351c-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="5351c-122">显示模式。</span><span class="sxs-lookup"><span data-stu-id="5351c-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="5351c-123">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="5351c-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="5351c-124">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="5351c-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="5351c-125">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="5351c-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="5351c-126">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="5351c-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="5351c-127">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="5351c-127">Reconnection rejected.</span></span> <span data-ttu-id="5351c-128">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="5351c-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="5351c-129">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="5351c-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="5351c-130">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="5351c-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="5351c-131">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="5351c-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="5351c-132">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="5351c-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="5351c-133">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="5351c-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="5351c-134">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="5351c-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="5351c-135">呈现模式</span><span class="sxs-lookup"><span data-stu-id="5351c-135">Render mode</span></span>

<span data-ttu-id="5351c-136">本部分适用于 Blazor 服务器。\*\*</span><span class="sxs-lookup"><span data-stu-id="5351c-136">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5351c-137">默认情况下，Blazor 服务器应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="5351c-137">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="5351c-138">这是在 _Host.cshtml\*\* Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="5351c-138">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="5351c-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="5351c-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="5351c-140">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="5351c-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="5351c-141">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="5351c-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="5351c-142">描述</span><span class="sxs-lookup"><span data-stu-id="5351c-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5351c-143">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5351c-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5351c-144">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5351c-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5351c-145">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5351c-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5351c-146">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="5351c-146">Output from the component isn't included.</span></span> <span data-ttu-id="5351c-147">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5351c-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5351c-148">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="5351c-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="5351c-149">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="5351c-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="5351c-150">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="5351c-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="5351c-151">本部分适用于 Blazor 服务器。\*\*</span><span class="sxs-lookup"><span data-stu-id="5351c-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5351c-152">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="5351c-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="5351c-153">例如，可能需要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="5351c-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="5351c-154">若要在 Pages/_Host.cshtml 文件中配置 SignalR 客户端，请执行以下操作\*\*：</span><span class="sxs-lookup"><span data-stu-id="5351c-154">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="5351c-155">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="5351c-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="5351c-156">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="5351c-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="5351c-157">其他资源</span><span class="sxs-lookup"><span data-stu-id="5351c-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
