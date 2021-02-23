---
title: ASP.NET Core Blazor SignalR 指南
author: guardrex
description: 了解如何配置和管理 Blazor SignalR 连接。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279860"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="62b6a-103">ASP.NET Core Blazor SignalR 指南</span><span class="sxs-lookup"><span data-stu-id="62b6a-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="62b6a-104">有关 ASP.NET Core SignalR 配置的常规指南，请参阅文档的 <xref:signalr/introduction> 区域中的主题。</span><span class="sxs-lookup"><span data-stu-id="62b6a-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="62b6a-105">若要配置[添加到托管的 Blazor WebAssembly 解决方案](xref:tutorials/signalr-blazor)的 SignalR，请参阅 <xref:signalr/configuration#configure-server-options>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="62b6a-106">线路处理程序选项</span><span class="sxs-lookup"><span data-stu-id="62b6a-106">Circuit handler options</span></span>

<span data-ttu-id="62b6a-107">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="62b6a-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="62b6a-108">使用下表所示的 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 配置 Blazor Server 线路。</span><span class="sxs-lookup"><span data-stu-id="62b6a-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="62b6a-109">选项</span><span class="sxs-lookup"><span data-stu-id="62b6a-109">Option</span></span> | <span data-ttu-id="62b6a-110">默认</span><span class="sxs-lookup"><span data-stu-id="62b6a-110">Default</span></span> | <span data-ttu-id="62b6a-111">描述</span><span class="sxs-lookup"><span data-stu-id="62b6a-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="62b6a-112">当线路上发生未经处理的异常时，或者通过 JS 互操作的 .NET 方法调用导致异常时，便向 JavaScript 发送详细的异常消息。</span><span class="sxs-lookup"><span data-stu-id="62b6a-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="62b6a-113">100</span><span class="sxs-lookup"><span data-stu-id="62b6a-113">100</span></span> | <span data-ttu-id="62b6a-114">给定服务器在内存中一次保留的断开连接的线路数上限。</span><span class="sxs-lookup"><span data-stu-id="62b6a-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="62b6a-115">3 分钟</span><span class="sxs-lookup"><span data-stu-id="62b6a-115">3 minutes</span></span> | <span data-ttu-id="62b6a-116">断开连接的线路被移除前在内存中保留的最长时间。</span><span class="sxs-lookup"><span data-stu-id="62b6a-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="62b6a-117">1 分钟</span><span class="sxs-lookup"><span data-stu-id="62b6a-117">1 minute</span></span> | <span data-ttu-id="62b6a-118">服务器在使异步 JavaScript 函数调用超时之前等待的最长时间。</span><span class="sxs-lookup"><span data-stu-id="62b6a-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="62b6a-119">10</span><span class="sxs-lookup"><span data-stu-id="62b6a-119">10</span></span> | <span data-ttu-id="62b6a-120">在给定时间内，服务器在内存中对每条线路保留用以支持重新连接可靠的未确认的呈现批处理的最大数量。</span><span class="sxs-lookup"><span data-stu-id="62b6a-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="62b6a-121">达到限制后，服务器会停止生成新的呈现批处理，直到客户端确认了一个或多个批处理。</span><span class="sxs-lookup"><span data-stu-id="62b6a-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="62b6a-122">使用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 的选项委托配置 `Startup.ConfigureServices` 中的选项。</span><span class="sxs-lookup"><span data-stu-id="62b6a-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="62b6a-123">下面的示例将分配上表中显示的默认选项值：</span><span class="sxs-lookup"><span data-stu-id="62b6a-123">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="62b6a-124">若要配置 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>，请结合使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> 和 <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="62b6a-125">有关选项说明，请参阅 <xref:signalr/configuration#configure-server-options>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="62b6a-126">以下示例分配默认选项值：</span><span class="sxs-lookup"><span data-stu-id="62b6a-126">The following example assigns the default option values:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="62b6a-127">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="62b6a-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="62b6a-128">本部分适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="62b6a-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="62b6a-129">若要将 SignalR 的基础客户端配置为发送凭据（如 cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="62b6a-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="62b6a-130">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 提取请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：</span><span class="sxs-lookup"><span data-stu-id="62b6a-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="62b6a-131">将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="62b6a-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="62b6a-132">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="62b6a-133">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="62b6a-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="62b6a-134">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="62b6a-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="62b6a-135">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="62b6a-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="62b6a-136">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="62b6a-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="62b6a-137">若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="62b6a-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="62b6a-138">向应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="62b6a-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="62b6a-139">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="62b6a-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="62b6a-140">CSS 类</span><span class="sxs-lookup"><span data-stu-id="62b6a-140">CSS class</span></span>                       | <span data-ttu-id="62b6a-141">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="62b6a-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="62b6a-142">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="62b6a-142">A lost connection.</span></span> <span data-ttu-id="62b6a-143">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="62b6a-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="62b6a-144">显示模式。</span><span class="sxs-lookup"><span data-stu-id="62b6a-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="62b6a-145">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="62b6a-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="62b6a-146">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="62b6a-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="62b6a-147">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="62b6a-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="62b6a-148">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="62b6a-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="62b6a-149">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="62b6a-149">Reconnection rejected.</span></span> <span data-ttu-id="62b6a-150">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="62b6a-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="62b6a-151">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="62b6a-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="62b6a-152">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="62b6a-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="62b6a-153">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="62b6a-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="62b6a-154">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="62b6a-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="62b6a-155">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="62b6a-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="62b6a-156">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="62b6a-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="62b6a-157">呈现模式</span><span class="sxs-lookup"><span data-stu-id="62b6a-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="62b6a-158">*此部分适用于托管的 Blazor WebAssembly 和 Blazor Server。*</span><span class="sxs-lookup"><span data-stu-id="62b6a-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="62b6a-159">Blazor 默认设置为在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="62b6a-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="62b6a-160">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="62b6a-161">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="62b6a-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="62b6a-162">默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="62b6a-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="62b6a-163">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="62b6a-164">初始化 Blazor 回路</span><span class="sxs-lookup"><span data-stu-id="62b6a-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="62b6a-165">本部分适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="62b6a-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="62b6a-166">在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用 [SignalR 回路 ](xref:blazor/hosting-models#circuits) 的手动启动：</span><span class="sxs-lookup"><span data-stu-id="62b6a-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="62b6a-167">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="62b6a-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="62b6a-168">将调用 `Blazor.start` 的脚本放置在 `blazor.server.js` 脚本标记之后并放在结束的 `</body>` 标记内。</span><span class="sxs-lookup"><span data-stu-id="62b6a-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="62b6a-169">禁用 `autostart` 时，应用中不依赖该回路的任何方面都能正常工作。</span><span class="sxs-lookup"><span data-stu-id="62b6a-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="62b6a-170">例如，客户端路由正常运行。</span><span class="sxs-lookup"><span data-stu-id="62b6a-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="62b6a-171">但是，在调用 `Blazor.start` 之前，依赖于该回路的任何方面不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="62b6a-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="62b6a-172">如果没有已建立的回路，应用行为是不可预测的。</span><span class="sxs-lookup"><span data-stu-id="62b6a-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="62b6a-173">例如，在回路断开连接时，组件方法无法执行。</span><span class="sxs-lookup"><span data-stu-id="62b6a-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="62b6a-174">文档准备就绪时初始化 Blazor</span><span class="sxs-lookup"><span data-stu-id="62b6a-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="62b6a-175">文档准备就绪时初始化 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="62b6a-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="62b6a-176">链接到由手动启动生成的 `Promise`</span><span class="sxs-lookup"><span data-stu-id="62b6a-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="62b6a-177">若要执行其他任务（如 JS 互操作初始化），请使用 `then` 链接到 `Promise`（由手动 Blazor 应用启动生成）：</span><span class="sxs-lookup"><span data-stu-id="62b6a-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="62b6a-178">配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="62b6a-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="62b6a-179">日志记录</span><span class="sxs-lookup"><span data-stu-id="62b6a-179">Logging</span></span>

<span data-ttu-id="62b6a-180">若要配置 SignalR 客户端日志，请传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别：</span><span class="sxs-lookup"><span data-stu-id="62b6a-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="62b6a-181">在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="62b6a-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="62b6a-182">修改重新连接处理程序</span><span class="sxs-lookup"><span data-stu-id="62b6a-182">Modify the reconnection handler</span></span>

<span data-ttu-id="62b6a-183">可以针对自定义行为修改重新连接处理程序的线路连接事件，如：</span><span class="sxs-lookup"><span data-stu-id="62b6a-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="62b6a-184">在连接断开时通知用户。</span><span class="sxs-lookup"><span data-stu-id="62b6a-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="62b6a-185">在线路连接时（通过客户端）执行日志记录。</span><span class="sxs-lookup"><span data-stu-id="62b6a-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="62b6a-186">若要修改连接事件，请为以下连接更改注册回调：</span><span class="sxs-lookup"><span data-stu-id="62b6a-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="62b6a-187">使用 `onConnectionDown` 删除的连接。</span><span class="sxs-lookup"><span data-stu-id="62b6a-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="62b6a-188">已建立/重新建立的连接使用 `onConnectionUp`。</span><span class="sxs-lookup"><span data-stu-id="62b6a-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="62b6a-189">必须同时指定 `onConnectionDown` 和 `onConnectionUp`：</span><span class="sxs-lookup"><span data-stu-id="62b6a-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="62b6a-190">调整重新连接重试计数和间隔</span><span class="sxs-lookup"><span data-stu-id="62b6a-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="62b6a-191">若要调整重新连接重试次数和间隔，请设置重试次数 (`maxRetries`) 和允许每次重试运行的毫秒数 (`retryIntervalMilliseconds`)：</span><span class="sxs-lookup"><span data-stu-id="62b6a-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="62b6a-192">隐藏或替换重新连接显示</span><span class="sxs-lookup"><span data-stu-id="62b6a-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="62b6a-193">若要隐藏重新连接显示，请将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）：</span><span class="sxs-lookup"><span data-stu-id="62b6a-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="62b6a-194">若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：</span><span class="sxs-lookup"><span data-stu-id="62b6a-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="62b6a-195">占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。</span><span class="sxs-lookup"><span data-stu-id="62b6a-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="62b6a-196">通过在应用的 CSS (`wwwroot/css/site.css`) 中为模式元素设置 `transition-delay` 属性，自定义重新连接显示出现之前的延迟。</span><span class="sxs-lookup"><span data-stu-id="62b6a-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="62b6a-197">以下示例将转换延迟从 500 毫秒（默认值）设置为 1000 毫秒（1 秒）：</span><span class="sxs-lookup"><span data-stu-id="62b6a-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="62b6a-198">从客户端断开 Blazor 线路连接</span><span class="sxs-lookup"><span data-stu-id="62b6a-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="62b6a-199">默认情况下，触发 [`unload` 页面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)时，Blazor 线路会断开连接。</span><span class="sxs-lookup"><span data-stu-id="62b6a-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="62b6a-200">若要断开客户端上其他方案的线路连接，请在相应的事件处理程序中调用 `Blazor.disconnect`。</span><span class="sxs-lookup"><span data-stu-id="62b6a-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="62b6a-201">在下面的示例中，当页面隐藏（[`pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)）时，线路会断开连接：</span><span class="sxs-lookup"><span data-stu-id="62b6a-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

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

## <a name="additional-resources"></a><span data-ttu-id="62b6a-202">其他资源</span><span class="sxs-lookup"><span data-stu-id="62b6a-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="62b6a-203">Blazor Server 重新连接事件和组件生命周期事件</span><span class="sxs-lookup"><span data-stu-id="62b6a-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
