---
title: 将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用
author: guardrex
description: 了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056252"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a>将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 组件可以集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

[准备好应用](#prepare-the-app)后，根据应用的要求按照下列部分中的指南操作：

* 可路由组件：针对可直接通过用户请求进行路由的组件。 如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。
  * [在 Razor Pages 应用中使用可路由组件](#use-routable-components-in-a-razor-pages-app)
  * [在 MVC 应用中使用可路由组件](#use-routable-components-in-an-mvc-app)
* [从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。 如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。

## <a name="prepare-the-app"></a>准备应用

现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：

1. 在应用的布局文件 (`_Layout.cshtml`) 中：

   * 将以下 `<base>` 标记添加到 `<head>` 元素：

     ```html
     <base href="~/" />
     ```

     前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。 如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。

     `_Layout.cshtml` 文件位于 Razor Pages 应用的 Pages/Shared 文件夹中，或 MVC 应用的 Views/Shared 文件夹中 。

   * 紧接在 `</body>` 结束标记前面，添加 blazor.server.js 脚本的 `<script>` 标记：

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     框架会将 blazor.server.js 脚本添加到应用。 无需手动将脚本添加到应用。

1. 将 `_Imports.razor` 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：

   ```razor
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

1. 在 `Startup.Configure` 中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 将组件集成到任何页面或视图。 有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>在 Razor Pages 应用中使用可路由组件

本部分介绍如何添加可直接从用户请求路由的组件。

在 Razor Pages 应用中支持可路由 Razor 组件：

1. 按照[准备应用](#prepare-the-app)部分中的指南进行操作。

1. 将具有以下内容的 `App.razor` 文件添加到项目根：

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 将具有以下内容的 `_Host.cshtml` 文件添加到 `Pages` 文件夹：

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 `_Layout.cshtml` 文件用于布局。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：

   * 在页面中预呈现。
   * 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

   | 呈现模式 | 描述 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 在静态 HTML 中呈现 `App` 组件，并包含 Blazor Server 应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor Server 应用的标记。 不包括 `App` 组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 在静态 HTML 中呈现 `App` 组件。 |

   要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

1. 在 `Startup.Configure` 中，将 `_Host.cshtml` 页的低优先级路由添加到终结点配置：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 将可路由组件添加到应用。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。

## <a name="use-routable-components-in-an-mvc-app"></a>在 MVC 应用中使用可路由组件

本部分介绍如何添加可直接从用户请求路由的组件。

在 MVC 应用中支持可路由 Razor 组件：

1. 按照[准备应用](#prepare-the-app)部分中的指南进行操作。

1. 将具有以下内容的 `App.razor` 文件添加到项目根：

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 将具有以下内容的 `_Host.cshtml` 文件添加到 `Views/Home` 文件夹：

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 `_Layout.cshtml` 文件用于布局。
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：

   * 在页面中预呈现。
   * 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

   | 呈现模式 | 描述 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 在静态 HTML 中呈现 `App` 组件，并包含 Blazor Server 应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor Server 应用的标记。 不包括 `App` 组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 在静态 HTML 中呈现 `App` 组件。 |

   要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

1. 向主控制器添加操作：

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 在 `Startup.Configure` 中，将返回 `_Host.cshtml` 视图的控制器操作的低优先级路由添加到终结点配置：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. 创建 `Pages` 文件夹并将可路由组件添加到应用。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。

## <a name="render-components-from-a-page-or-view"></a>从页面或视图呈现组件

本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。

若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。

### <a name="render-stateful-interactive-components"></a>呈现有状态交互式组件

可以将有状态的交互式组件添加到 Razor 页面或视图。

呈现页面或视图时：

* 该组件通过页面或视图预呈现。
* 用于预呈现的初始组件状态丢失。
* 建立 SignalR 连接时，将创建新的组件状态。

以下 Razor 页面将呈现 `Counter` 组件：

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

### <a name="render-noninteractive-components"></a>呈现非交互式组件

在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。 由于该组件是以静态方式呈现的，因此它不是交互式组件：

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

## <a name="component-namespaces"></a>组件命名空间

使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 `_ViewImports.cshtml` 文件。 如下示例中：

* 将 `MyAppNamespace` 更改为应用的命名空间。
* 如果不使用名为 Components 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml` 文件位于 Razor Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。

有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。
