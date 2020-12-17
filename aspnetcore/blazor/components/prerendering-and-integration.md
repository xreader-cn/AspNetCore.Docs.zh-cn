---
title: 预呈现和集成 ASP.NET Core Razor 组件
author: guardrex
description: 了解 Blazor 应用的 Razor 组件集成方案，包括在服务器上预呈现 Razor 组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506976"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>预呈现和集成 ASP.NET Core Razor 组件

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor 组件可以通过托管的 Blazor WebAssembly 解决方案集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

## <a name="configuration"></a>Configuration

设置 Blazor WebAssembly 应用的预呈现：

1. 将 Blazor WebAssembly 应用托管在 ASP.NET Core 应用中。 可以将独立的 Blazor WebAssembly 应用添加到 ASP.NET Core 解决方案中，也可以使用 Blazor 托管项目模板创建的托管 Blazor WebAssembly 应用。

1. 从 Blazor WebAssembly 客户端项目中删除默认的静态 `wwwroot/index.html` 文件。

1. 删除客户端项目的 `Program.Main` 中的以下行：

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. 将 `Pages/_Host.cshtml` 文件添加到服务器项目。 可以使用命令行界面中的 `dotnet new blazorserver -o BlazorServer` 命令获得 Blazor Server 模板创建的应用的 `_Host.cshtml` 文件。 将 `Pages/_Host.cshtml` 文件放入托管 Blazor WebAssembly 解决方案的服务器应用后，对此文件进行以下更改：

   * 将命名空间设置为服务器应用的 `Pages` 文件夹（例如，`@namespace BlazorHosted.Server.Pages`）。
   * 为客户端项目设置 [`@using`](xref:mvc/views/razor#using) 指令（例如 `@using BlazorHosted.Client`）。
   * 更新样式表链接，以指向 WebAssembly 应用的样式表。 在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * 更新[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode`，以预呈现根 `App` 组件：

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * 更新 Blazor 脚本源，以使用客户端 Blazor WebAssembly 脚本：

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. 在服务器项目的 `Startup.Configure` (`Startup.cs`) 中：

   * 在开发环境中从应用程序生成器上调用 `UseDeveloperExceptionPage`。
   * 在应用程序生成器上调用 `UseBlazorFrameworkFiles`。
   * 将回退从 `index.html` 页面 (`endpoints.MapFallbackToFile("index.html");`) 更改为 `_Host.cshtml` 页面。

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>使用组件标记帮助程序，通过页面或视图呈现组件

组件标记帮助程序支持两种呈现模式来通过页面或视图呈现 Blazor WebAssembly 应用中的组件：

* `WebAssembly`：呈现 Blazor WebAssembly 应用程序的标记，以便在浏览器中进行加载时包含交互式组件。 不预呈现组件。 通过此选项，可以在不同的页面轻松呈现各种 Blazor WebAssembly 组件。
* `WebAssemblyPrerendered`：在静态 HTML 中预呈现组件，并包含 Blazor WebAssembly 应用的标记，以便稍后在浏览器中进行加载时使组件成为交互式组件。

在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。 页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。 为客户端应用的 `Pages` 命名空间添加 [`@using`](xref:mvc/views/razor#using) 指令，以避免将 `Counter` 组件的整个命名空间用于组件标记帮助程序 (`{APP ASSEMBLY}.Pages.Counter`)。 在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：

* 在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

前面的示例要求服务器应用的布局 (`_Layout.cshtml`) 在 `</body>` 结束标记内包含脚本的[呈现部分](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)：

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

`_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。

如果应用程序还应使用 Blazor WebAssembly 应用中的样式设置组件样式，则将此应用的样式包含在 `_Layout.cshtml` 文件中。 在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>使用 CSS 选择器，通过页面或视图呈现组件

将根组件添加到 `Program.Main` (`Program.cs`) 中的“客户端”项目中。 下面的示例使用 CSS 选择器将 `Counter` 组件声明为根组件，该选择器会选择 `id` 与 `my-counter` 匹配的元素。 在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。 页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件：

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

前面的示例要求服务器应用的布局 (`_Layout.cshtml`) 在 `</body>` 结束标记内包含脚本的[呈现部分](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)：

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

`_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。

如果应用程序还应使用 Blazor WebAssembly 应用中的样式设置组件样式，则将此应用的样式包含在 `_Layout.cshtml` 文件中。 在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

.NET 5 或更高版本中的 ASP.NET Core 支持通过托管的 Blazor WebAssembly 解决方案将 Razor 组件集成到 Razor Pages 和 MVC 应用。 请选择本文的 .NET 5 或更高版本。

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor 组件可以通过 Blazor Server 应用集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

[配置此应用](#configuration)后，根据应用的要求按照下列部分中的指南操作：

* 可路由组件：针对可直接通过用户请求进行路由的组件。 如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。
  * [在 Razor Pages 应用中使用可路由组件](#use-routable-components-in-a-razor-pages-app)
  * [在 MVC 应用中使用可路由组件](#use-routable-components-in-an-mvc-app)
* [从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。 如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。

## <a name="configuration"></a>Configuration

现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：

1. 在应用的布局文件 (`_Layout.cshtml`) 中：

   * 将以下 `<base>` 标记添加到 `<head>` 元素：

     ```html
     <base href="~/" />
     ```

     前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。 如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。

     `_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。

   * 在紧接着 `Scripts` 呈现部分的前方为 `blazor.server.js` 脚本添加 `<script>` 标记：

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     框架会将 `blazor.server.js` 脚本添加到应用。 无需手动将脚本添加到应用。

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

1. 按照[配置](#configuration)部分中的指南操作。

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

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

1. 按照[配置](#configuration)部分中的指南操作。

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

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
* 如果不使用名为 `Components` 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml` 文件位于 Razor Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。

有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。

::: zone-end
