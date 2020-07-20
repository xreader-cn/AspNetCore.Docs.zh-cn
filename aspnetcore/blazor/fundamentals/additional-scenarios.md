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
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239629"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 托管模型配置

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

本文介绍了托管模型配置。

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>用于身份验证的 SignalR 跨源协商

本部分适用于 Blazor WebAssembly。

若要将 SignalR 的基础客户端配置为发送凭据（如 Cookie 或 HTTP 身份验证标头），请执行以下操作：

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

本部分适用于 Blazor Server。

默认情况下，Blazor Server 应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。 这是在 `_Host.cshtml` Razor 页中设置的：

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：

* 在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

| 呈现模式 | 描述 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 在静态 HTML 中呈现组件，并包含 Blazor Server 应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor Server 应用的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 将组件呈现为静态 HTML。 |

不支持从静态 HTML 页面呈现服务器组件。

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>为 Blazor Server 应用配置 SignalR 客户端

本部分适用于 Blazor Server。

在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用使用的 SignalR 客户端。 将调用 `Blazor.start` 的脚本放置在 `_framework/blazor.server.js` 脚本之后的 `</body>` 标记内。

### <a name="logging"></a>Logging

若要配置 SignalR 客户端日志记录，请执行以下操作：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 传入调用 `configureLogging` 的配置对象 (`configureSignalR`)，此对象在客户端生成器上具有日志级别。

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

若要修改连接事件，请执行以下操作：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 为断开的连接 (`onConnectionDown`) 和建立/重新建立的连接 (`onConnectionUp`) 注册连接更改回叫。 必须同时指定 `onConnectionDown` 和 `onConnectionUp`。

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>调整重新连接重试计数和间隔

若要调整重新连接重试计数和间隔，请执行以下操作：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 设置允许的每次重试尝试 (`retryIntervalMilliseconds`) 的重试次数 (`maxRetries`) 和时间间隔（以毫秒为单位）。

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="hide-or-replace-the-reconnection-display"></a>隐藏或替换重新连接显示

若要隐藏重新连接显示，请执行以下操作：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）。

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/index>
