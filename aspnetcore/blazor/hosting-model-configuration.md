---
title: ASP.NET Core Blazor 宿主模型配置
author: guardrex
description: 了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214939"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 宿主模型配置

作者： [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

本文介绍了托管模型配置。

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor 服务器

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>将 Razor 组件集成到 Razor Pages 和 MVC 应用

#### <a name="use-components-in-pages-and-views"></a>使用页面和视图中的组件

现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：

1. 在应用的布局文件（ *_Layout cshtml*）中：

   * 将以下 `<base>` 标记添加到 `<head>` 元素：

     ```html
     <base href="~/" />
     ```

     上一示例中的 `href` 值（*应用程序基路径*）假定应用位于根 URL 路径（`/`）。 如果应用是子应用程序，请按照 <xref:host-and-deploy/blazor/index#app-base-path> 文章的*应用程序基路径*部分中的指导进行操作。

     *_Layout*的文件位于一个 Razor Pages 应用中的*Pages/shared*文件夹中，或位于 MVC 应用程序的*视图/共享*文件夹中。

   * 在关闭 `</body>` 标记之前，添加*blazor*脚本的 `<script>` 标记：

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     框架将*blazor*脚本添加到应用程序。 无需手动将脚本添加到应用。

1. 将 *_Imports razor*文件添加到具有以下内容的项目的根文件夹中（将最后一个命名空间 `MyAppNamespace`更改为应用的命名空间）：

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. 在 `Startup.ConfigureServices`中，注册 Blazor Server 服务：

   ```csharp
   services.AddServerSideBlazor();
   ```

1. 在 `Startup.Configure`中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 将组件集成到任何页面或视图中。 有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> 文章的将*组件集成到 Razor Pages 和 MVC 应用程序*部分。

#### <a name="use-routable-components-in-a-razor-pages-app"></a>在 Razor Pages 应用程序中使用可路由组件

支持 Razor Pages 应用中的可路由 Razor 组件：

1. 按照[使用页面和视图中的组件](#use-components-in-pages-and-views)部分中的指导进行操作。

1. 将一个*app.config*文件添加到项目的根目录，其中包含以下内容：

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 将 *_Host 的 cshtml*文件添加到*Pages*文件夹，其中包含以下内容：

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件使用共享的 *_Layout cshtml*文件进行布局。

1. 将 *_Host cshtml*页的低优先级路由添加到 `Startup.Configure`中的终结点配置：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 将可路由的组件添加到应用。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   使用自定义文件夹保存应用程序的组件时，将表示文件夹的命名空间添加到*Pages/_ViewImports cshtml*文件中。 有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>。

#### <a name="use-routable-components-in-an-mvc-app"></a>在 MVC 应用中使用可路由组件

在 MVC 应用中支持可路由的 Razor 组件：

1. 按照[使用页面和视图中的组件](#use-components-in-pages-and-views)部分中的指导进行操作。

1. 将一个*app.config*文件添加到项目的根目录，其中包含以下内容：

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 将 *_Host 的 cshtml*文件添加到具有以下内容的*Views/Home*文件夹中：

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件使用共享的 *_Layout cshtml*文件进行布局。

1. 向 Home 控制器添加操作：

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 将返回 *_Host*的控制器操作的低优先级路由添加到 `Startup.Configure`中的终结点配置：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. 创建*Pages*文件夹，并将可路由的组件添加到应用。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   使用自定义文件夹保存应用程序的组件时，将表示文件夹的命名空间添加到*Views/_ViewImports cshtml*文件中。 有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>。

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
