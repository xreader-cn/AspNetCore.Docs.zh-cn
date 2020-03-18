---
title: 将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用
author: guardrex
description: 了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649080"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 组件可以集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>准备应用以在页面和视图中使用组件

现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：

1. 在应用的布局文件 (_Layout.cshtml) 中：

   * 将以下 `<base>` 标记添加到 `<head>` 元素：

     ```html
     <base href="~/" />
     ```

     前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。 如果应用是子应用程序，请按照 <xref:host-and-deploy/blazor/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。

     _Layout.cshtml 文件位于 Razor Pages 应用的 Pages/Shared 文件夹中，或 MVC 应用的 Views/Shared 文件夹中。

   * 紧接在 `</body>` 结束标记前面，添加 blazor.server.js 脚本的 `<script>` 标记：

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     框架会将 blazor.server.js 脚本添加到应用。 无需手动将脚本添加到应用。

1. 将 _Imports.razor 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：

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

1. 在 `Startup.ConfigureServices` 中，注册 Blazor 服务器服务：

   ```csharp
   services.AddServerSideBlazor();
   ```

1. 在 `Startup.Configure` 中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 将组件集成到任何页面或视图。 有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。

## <a name="use-routable-components-in-a-razor-pages-app"></a>在 Razor Pages 应用中使用可路由组件

本部分介绍如何添加可直接从用户请求路由的组件。

在 Razor Pages 应用中支持可路由 Razor 组件：

1. 按照[准备应用以在页面和视图中使用组件](#prepare-the-app-to-use-components-in-pages-and-views)部分中的指导进行操作。

1. 将具有以下内容的 App.razor 文件添加到项目根：

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

1. 将具有以下内容的 _Host.cshtml 文件添加到 Pages 文件夹：

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 _Layout.cshtml 文件用于布局。

1. 在 `Startup.Configure` 中，将 _Host.cshtml 的低优先级路由添加到终结点配置：

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

1. 按照[准备应用以在页面和视图中使用组件](#prepare-the-app-to-use-components-in-pages-and-views)部分中的指导进行操作。

1. 将具有以下内容的 App.razor 文件添加到项目根：

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

1. 将具有以下内容的 _Host.cshtml 文件添加到 Views/Home 文件夹：

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 _Layout.cshtml 文件用于布局。

1. 向主控制器添加操作：

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 在 `Startup.Configure` 中，将返回 _Host.cshtml 视图的控制器操作的低优先级路由添加到终结点配置：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. 创建 Pages 文件夹并将可路由组件添加到应用。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。

## <a name="component-namespaces"></a>组件命名空间

使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 _ViewImports.cshtml 文件。 如下示例中：

* 将 `MyAppNamespace` 更改为应用的命名空间。
* 如果不使用名为 Components 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。

```cshtml
@using MyAppNamespace.Components
```

_ViewImports.cshtml 文件位于 Razor Pages 应用的 Pages 文件夹中，或是 MVC 应用的 Views 文件夹中。

有关详细信息，请参阅 <xref:blazor/components#import-components>。

## <a name="render-components-from-a-page-or-view"></a>从页面或视图呈现组件

本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。

若要从页面或视图呈现组件，请使用 `Component` 标记帮助程序：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置的属性。 例如，可以指定 `IncrementAmount` 的值，因为 `IncrementAmount` 的类型为 `int`，这是 JSON 序列化程序所支持的基元类型。

`RenderMode` 配置组件是否：

* 已在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

| `RenderMode`        | 描述 |
| ------------------- | ----------- |
| `ServerPrerendered` | 将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| `Server`            | 呈现 Blazor 服务器应用的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| `Static`            | 将组件呈现为静态 HTML。 |

尽管页面和视图可以使用组件，但反过来则不行。 组件无法使用特定于视图和页面的方案，如分部视图和部分。 若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解到组件中。

不支持从静态 HTML 页面呈现服务器组件。

有关如何呈现组件、组件状态以及 `Component` 标记帮助程序的详细信息，请参阅以下文章：

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
