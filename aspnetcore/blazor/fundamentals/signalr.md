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
# <a name="aspnet-core-blazor-signalr-guidance"></a>ASP.NET Core Blazor SignalR 指南

有关 ASP.NET Core SignalR 配置的常规指南，请参阅文档的 <xref:signalr/introduction> 区域中的主题。 若要配置[添加到托管的 Blazor WebAssembly 解决方案](xref:tutorials/signalr-blazor)的 SignalR，请参阅 <xref:signalr/configuration#configure-server-options>。

## <a name="circuit-handler-options"></a>线路处理程序选项

本部分适用于 Blazor Server。

使用下表所示的 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 配置 Blazor Server 线路。

| 选项 | 默认 | 描述 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | 当线路上发生未经处理的异常时，或者通过 JS 互操作的 .NET 方法调用导致异常时，便向 JavaScript 发送详细的异常消息。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | 给定服务器在内存中一次保留的断开连接的线路数上限。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 分钟 | 断开连接的线路被移除前在内存中保留的最长时间。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 分钟 | 服务器在使异步 JavaScript 函数调用超时之前等待的最长时间。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | 在给定时间内，服务器在内存中对每条线路保留用以支持重新连接可靠的未确认的呈现批处理的最大数量。 达到限制后，服务器会停止生成新的呈现批处理，直到客户端确认了一个或多个批处理。 |

使用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 的选项委托配置 `Startup.ConfigureServices` 中的选项。 下面的示例将分配上表中显示的默认选项值：

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

若要配置 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>，请结合使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> 和 <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>。 有关选项说明，请参阅 <xref:signalr/configuration#configure-server-options>。 以下示例分配默认选项值：

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>用于身份验证的 SignalR 跨源协商

本部分适用于 Blazor WebAssembly。

若要将 SignalR 的基础客户端配置为发送凭据（如 cookie 或 HTTP 身份验证标头），请执行以下操作：

* 使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 提取请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>：

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

* 将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。

## <a name="reflect-the-connection-state-in-the-ui"></a>反映 UI 中的连接状态

本部分适用于 Blazor Server。

如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。 如果重新连接失败，则会向用户提供重试选项。

若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

向应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）添加以下内容：

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。

| CSS 类                       | 指示&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 连接已丢失。 客户端正在尝试重新连接。 显示模式。 |
| `components-reconnect-hide`     | 将为服务器重新建立活动连接。 隐藏模式。 |
| `components-reconnect-failed`   | 重新连接失败，可能是由于网络故障引起的。 若要尝试重新连接，请调用 `window.Blazor.reconnect()`。 |
| `components-reconnect-rejected` | 已拒绝重新连接。 已达到服务器，但拒绝连接，服务器上的用户状态丢失。 若要重载应用，请调用 `location.reload()`。 当出现以下情况时，可能会导致此连接状态：<ul><li>服务器端线路发生故障。</li><li>客户端断开连接的时间足以使服务器删除用户的状态。 已释放用户正在与之交互的组件的实例。</li><li>服务器已重启，或者应用的工作进程被回收。</li></ul> |

## <a name="render-mode"></a>呈现模式

::: moniker range=">= aspnetcore-5.0"

*此部分适用于托管的 Blazor WebAssembly 和 Blazor Server。*

Blazor 默认设置为在服务器上预呈现 UI。 有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

本部分适用于 Blazor Server。

默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。 有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a>初始化 Blazor 回路

本部分适用于 Blazor Server。

在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用 [SignalR 回路 ](xref:blazor/hosting-models#circuits) 的手动启动：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 将调用 `Blazor.start` 的脚本放置在 `blazor.server.js` 脚本标记之后并放在结束的 `</body>` 标记内。

禁用 `autostart` 时，应用中不依赖该回路的任何方面都能正常工作。 例如，客户端路由正常运行。 但是，在调用 `Blazor.start` 之前，依赖于该回路的任何方面不会正常运行。 如果没有已建立的回路，应用行为是不可预测的。 例如，在回路断开连接时，组件方法无法执行。

### <a name="initialize-blazor-when-the-document-is-ready"></a>文档准备就绪时初始化 Blazor

文档准备就绪时初始化 Blazor 应用：

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>链接到由手动启动生成的 `Promise`

若要执行其他任务（如 JS 互操作初始化），请使用 `then` 链接到 `Promise`（由手动 Blazor 应用启动生成）：

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

### <a name="configure-the-signalr-client"></a>配置 SignalR 客户端

#### <a name="logging"></a>日志记录

若要配置 SignalR 客户端日志，请传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别：

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

在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。

### <a name="modify-the-reconnection-handler"></a>修改重新连接处理程序

可以针对自定义行为修改重新连接处理程序的线路连接事件，如：

* 在连接断开时通知用户。
* 在线路连接时（通过客户端）执行日志记录。

若要修改连接事件，请为以下连接更改注册回调：

* 使用 `onConnectionDown` 删除的连接。
* 已建立/重新建立的连接使用 `onConnectionUp`。

必须同时指定 `onConnectionDown` 和 `onConnectionUp`：

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>调整重新连接重试计数和间隔

若要调整重新连接重试次数和间隔，请设置重试次数 (`maxRetries`) 和允许每次重试运行的毫秒数 (`retryIntervalMilliseconds`)：

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

## <a name="hide-or-replace-the-reconnection-display"></a>隐藏或替换重新连接显示

若要隐藏重新连接显示，请将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）：

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

若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。

::: moniker range=">= aspnetcore-5.0"

通过在应用的 CSS (`wwwroot/css/site.css`) 中为模式元素设置 `transition-delay` 属性，自定义重新连接显示出现之前的延迟。 以下示例将转换延迟从 500 毫秒（默认值）设置为 1000 毫秒（1 秒）：

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>从客户端断开 Blazor 线路连接

默认情况下，触发 [`unload` 页面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)时，Blazor 线路会断开连接。 若要断开客户端上其他方案的线路连接，请在相应的事件处理程序中调用 `Blazor.disconnect`。 在下面的示例中，当页面隐藏（[`pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)）时，线路会断开连接：

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

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server 重新连接事件和组件生命周期事件](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
