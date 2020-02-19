---
title: ASP.NET Core Blazor 宿主模型配置
author: guardrex
description: 了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447030"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 宿主模型配置

作者： [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

本文介绍了托管模型配置。

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor 服务器

### <a name="reflect-the-connection-state-in-the-ui"></a>反映 UI 中的连接状态

当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。 如果重新连接失败，则会向用户提供重试选项。

若要自定义 UI，请在 *_Host* Razor page 的 `<body>` 中定义具有 `components-reconnect-modal` `id` 的元素：

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

下表描述了应用于 `components-reconnect-modal` 元素的 CSS 类。

| CSS 类                       | 指示&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 断开连接。 客户端正在尝试重新连接。 显示模式。 |
| `components-reconnect-hide`     | 将为服务器重新建立活动连接。 隐藏模式。 |
| `components-reconnect-failed`   | 重新连接失败，可能是由于网络故障引起的。 若要尝试重新连接，请调用 `window.Blazor.reconnect()`。 |
| `components-reconnect-rejected` | 已拒绝重新连接。 服务器已达到但拒绝了连接，并且服务器上的用户状态丢失。 若要重新加载应用，请调用 `location.reload()`。 当以下情况时，可能会导致此连接状态：<ul><li>服务器端线路发生崩溃。</li><li>断开客户端的连接时间足以使服务器删除用户的状态。 将释放用户与之交互的组件的实例。</li><li>服务器已重新启动，或者应用程序的工作进程被回收。</li></ul> |

### <a name="render-mode"></a>呈现模式

默认情况下，Blazor 服务器应用程序设置为在客户端与服务器建立连接之前，在服务器上预呈现 UI。 这是在 *_Host*的 "Razor" Razor 页面中设置的：

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` 配置组件是否：

* 已预呈现到页面中。
* 在页面上呈现为静态 HTML，或者，如果包含从用户代理启动 Blazor 应用所需的信息，则为。

| `RenderMode`        | 说明 |
| ------------------- | ----------- |
| `ServerPrerendered` | 将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| `Server`            | 呈现 Blazor 服务器应用程序的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| `Static`            | 将组件呈现为静态 HTML。 |

不支持从静态 HTML 页面呈现服务器组件。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>从 Razor 页面和视图呈现有状态交互式组件

可以将有状态交互组件添加到 Razor 页面或视图。

呈现页面或视图时：

* 该组件与页面或视图预呈现。
* 用于预呈现的初始组件状态将丢失。
* 建立 SignalR 连接时，将创建新的组件状态。

以下 Razor 页面将呈现一个 `Counter` 组件：

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>从 Razor 页面和视图呈现非交互式组件

在以下 Razor 页面中，使用以下格式使用指定的初始值静态呈现 `Counter` 组件：

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

由于 `MyComponent` 是以静态方式呈现的，因此该组件不能是交互式的。

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>为 Blazor 服务器应用配置 SignalR 客户端

有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。 例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。

若要在*Pages/_Host* # 文件中配置 SignalR 客户端：

* 将 `autostart="false"` 特性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。

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
