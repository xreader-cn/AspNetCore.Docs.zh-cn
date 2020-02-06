---
title: ASP.NET Core Blazor 宿主模型
author: guardrex
description: 了解 Blazor WebAssembly 和 Blazor 服务器托管模型。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7b4d4aca0bc4650c31bc8e5c4a84ecbad6a49b09
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034077"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 宿主模型

作者： [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 是一种 web 框架，旨在在基于[WebAssembly](https://webassembly.org/)的 .net 运行时（*Blazor WebAssembly*）上的浏览器中运行客户端，或在 ASP.NET Core 中的服务器端（*Blazor 服务器*）运行。 无论采用何种托管模型，应用和组件模型*都是相同*的。

若要为本文中所述的托管模型创建项目，请参阅 <xref:blazor/get-started>。

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Blazor 的主体托管模型在 WebAssembly 上的浏览器中运行客户端。 将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。 应用将在浏览器线程中直接执行。 UI 更新和事件处理发生在同一进程中。 应用的资产将作为静态文件部署到 web 服务器或可为客户端提供静态内容的服务。

![Blazor WebAssembly： Blazor 应用程序在浏览器内的 UI 线程上运行。](hosting-models/_static/blazor-webassembly.png)

若要使用客户端托管模型创建 Blazor 应用，请使用**Blazor WebAssembly 应用**模板（[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)）。

选择**Blazor WebAssembly 应用程序**模板之后，你可以选择将应用配置为使用 ASP.NET Core 后端，方法是选择 " **ASP.NET Core 托管**" 复选框（"[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)"）。 ASP.NET Core 应用程序将 Blazor 应用程序提供给客户端。 Blazor WebAssembly 应用可使用 web API 调用或[SignalR](xref:signalr/introduction) （<xref:tutorials/signalr-blazor-webassembly>）通过网络与服务器交互。

模板包括处理的 `blazor.webassembly.js` 脚本：

* 下载 .NET 运行时、应用程序和应用程序的依赖项。
* 用于运行应用程序的运行时初始化。

Blazor WebAssembly 托管模型具有以下几个优点：

* 没有 .NET 服务器端依赖项。 应用在下载到客户端之后完全正常运行。
* 完全利用客户端资源和功能。
* 工作从服务器卸载到客户端。
* 不需要 ASP.NET Core web 服务器来托管应用程序。 无服务器部署方案可能（例如，通过 CDN 提供应用）。

Blazor WebAssembly 托管有缺点：

* 应用程序限制为浏览器的功能。
* 需要支持的客户端硬件和软件（例如，WebAssembly 支持）。
* 下载大小较大，应用需要较长时间才能加载。
* .NET 运行时和工具支持不太成熟。 例如， [.NET Standard](/dotnet/standard/net-standard)支持和调试中存在限制。

## <a name="blazor-server"></a>Blazor 服务器

使用 Blazor 服务器托管模型，可在服务器上从 ASP.NET Core 应用中执行应用。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

![浏览器通过 SignalR 连接与服务器上 ASP.NET Core 应用程序内托管的应用程序进行交互。](hosting-models/_static/blazor-server.png)

若要使用 Blazor 服务器托管模型创建 Blazor 应用，请使用 ASP.NET Core **Blazor 服务器应用**模板（[dotnet new blazorserver](/dotnet/core/tools/dotnet-new)）。 ASP.NET Core 应用托管 Blazor 服务器应用，并创建客户端连接到的 SignalR 终结点。

ASP.NET Core 应用引用要添加的应用 `Startup` 类：

* 服务器端服务。
* 请求处理管道的应用。

`blazor.server.js` 脚本&dagger; 建立客户端连接。 应用负责根据需要保存和还原应用状态（例如，在网络连接丢失的情况下）。

Blazor 服务器托管模型具有以下几个优点：

* 下载大小明显小于 Blazor WebAssembly 应用，且应用加载速度快得多。
* 应用充分利用服务器功能，包括使用任何与 .NET Core 兼容的 Api。
* 服务器上的 .NET Core 用于运行应用程序，因此现有的 .NET 工具（如调试）可按预期方式工作。
* 支持瘦客户端。 例如，Blazor 服务器应用程序适用于不支持 WebAssembly 的浏览器以及资源受限设备上的浏览器。
* 应用程序的 .NET/C#代码库（包括应用程序的组件代码）不会提供给客户端。

Blazor 服务器托管有缺点：

* 通常存在较高的延迟。 每个用户交互都涉及网络跃点。
* 无脱机支持。 如果客户端连接失败，应用将停止工作。
* 对于包含多个用户的应用而言，可伸缩性非常困难。 服务器必须管理多个客户端连接并处理客户端状态。
* 为应用提供服务需要 ASP.NET Core 服务器。 不可能的无服务器部署方案（例如，通过 CDN 为应用提供服务）。

&dagger;`blazor.server.js` 脚本是从 ASP.NET Core 共享框架中的嵌入资源提供的。

### <a name="comparison-to-server-rendered-ui"></a>与服务器呈现的 UI 的比较

了解 Blazor 服务器应用程序的一种方法是了解它与传统模型的不同之处在于，使用 Razor 视图或 Razor Pages 在 ASP.NET Core 应用程序中呈现 UI。 这两种模型都使用 Razor 语言来描述 HTML 内容，但在显示标记的方式上差别很大。

在显示 Razor 页面或视图时，每行 Razor 代码都会以文本形式发出 HTML。 在呈现后，服务器将释放页面或视图实例，包括生成的任何状态。 当对该页的另一请求出现时，例如，当服务器验证失败并显示验证摘要时：

* 整个页面将再次重新呈现到 HTML 文本。
* 该页将发送到客户端。

Blazor 应用由 UI 的可重用元素组成，这些元素称为*组件*。 组件包含C#代码、标记和其他组件。 呈现组件时，Blazor 将生成包含的组件的图，类似于 HTML 或 XML 文档对象模型（DOM）。 此图包含属性和字段中保存的组件状态。 Blazor 计算组件图以生成标记的二进制表示形式。 二进制格式可以是：

* 转换为 HTML 文本（在预呈现期间）。
* 用于在常规呈现期间有效地更新标记。

Blazor 中的 UI 更新由以下用户触发：

* 用户交互，如选择一个按钮。
* 应用触发器，如计时器。

关系图为重新呈现，并计算了 UI*差异*（差异）。 这种差异是更新客户端上 UI 所需的最小 DOM 编辑集。 将以二进制格式将差异发送到客户端，并由浏览器应用。

当用户在客户端上导航掉组件后，将释放该组件。 当用户与组件交互时，组件的状态（服务、资源）必须保存在服务器的内存中。 由于多个组件的状态可能同时由服务器维护，因此内存耗尽是必须解决的问题。 有关如何创作 Blazor 服务器应用以确保最大程度地使用服务器内存的指导，请参阅 <xref:security/blazor/server>。

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

   * 在关闭 `</body>` 标记内添加*blazor*脚本的 `<script>` 标记：

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

1. 在 `Startup.ConfigureServices`中，添加 Blazor Server 服务：

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

### <a name="circuits"></a>而言

Blazor 服务器应用基于[ASP.NET Core SignalR](xref:signalr/introduction)构建。 每个客户端通过一个或多个称为*线路*的 SignalR 连接与服务器通信。 线路是 Blazor 对 SignalR 连接的抽象，可容忍临时网络中断。 当 Blazor 客户端发现 SignalR 连接已断开连接时，它会尝试使用新的 SignalR 连接重新连接到服务器。

连接到 Blazor 服务器应用的每个浏览器屏幕（浏览器选项卡或 iframe）都使用 SignalR 连接。 与典型服务器呈现的应用相比，这一点还有一个重要的区别。 在服务器呈现的应用程序中，在多个浏览器屏幕中打开同一应用程序通常不会转换为服务器上的其他资源需求。 在 Blazor 服务器应用中，每个浏览器屏幕都需要一个单独的线路，并将组件状态的实例单独置于服务器管理的状态。

Blazor 考虑关闭浏览器选项卡或导航到外部 URL，*正常*终止。 如果正常终止，则会立即释放线路和关联的资源。 例如，由于网络中断，客户端也可能断开连接。 Blazor 服务器会将断开连接的线路存储为可配置的间隔，以允许客户端重新连接。 有关详细信息，请参阅重新[连接到同一服务器](#reconnection-to-the-same-server)部分。

### <a name="ui-latency"></a>UI 延迟

UI 延迟是指从启动的操作到 UI 更新的时间。 对于应用程序来说，更小的 UI 延迟值非常适合应用程序对用户的响应。 在 Blazor 服务器应用中，每个操作都将发送到服务器，进行处理，并向后发送 UI 差异。 因此，UI 延迟是网络延迟和处理操作时服务器延迟的总和。

对于仅限于专用公司网络的业务线应用，对用户而言，由于网络延迟导致的延迟通常是让的。 对于通过 Internet 部署的应用，用户可能会对延迟造成明显的影响，尤其是用户广泛分散于各地。

内存使用率还会导致应用延迟。 增加的内存使用会导致频繁垃圾收集或将内存分页到磁盘，这两者都会降低应用程序性能，进而增加 UI 延迟。 有关详细信息，请参阅 <xref:security/blazor/server>。

应通过减少网络延迟和内存使用来优化 Blazor 服务器应用，从而最大限度地减少 UI 延迟。 有关测量网络延迟的方法，请参阅 <xref:host-and-deploy/blazor/server#measure-network-latency>。 有关 SignalR 和 Blazor 的详细信息，请参阅：

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>与服务器的连接

Blazor 服务器应用需要与服务器建立活动的 SignalR 连接。 如果连接丢失，应用会尝试重新连接到服务器。 只要客户端的状态仍在内存中，客户端会话便会恢复而不会失去状态。

#### <a name="reconnection-to-the-same-server"></a>重新连接到同一台服务器

Blazor 服务器应用 prerenders 响应第一个客户端请求，后者在服务器上设置 UI 状态。 当客户端尝试创建 SignalR 连接时，客户端必须重新连接到同一服务器。 使用多个后端服务器的 Blazor 服务器应用应为 SignalR 连接实现*粘滞会话*。

我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor 服务器应用。 该服务允许将 Blazor 服务器应用扩展到大量并发 SignalR 连接。 为 Azure SignalR 服务启用了粘滞会话，方法是将服务的 `ServerStickyMode` 选项或配置值设置为 `Required`。 有关详细信息，请参阅 <xref:host-and-deploy/blazor/server#signalr-configuration>。

使用 IIS 时，粘滞会话通过应用程序请求路由启用。 有关详细信息，请参阅[使用应用程序请求路由实现 HTTP 负载均衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。

#### <a name="reflect-the-connection-state-in-the-ui"></a>反映 UI 中的连接状态

当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。 如果重新连接失败，则会向用户提供重试选项。

若要自定义 UI，请在 *_Host* Razor page 的 `<body>` 中定义具有 `components-reconnect-modal` `id` 的元素：

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

下表描述了应用于 `components-reconnect-modal` 元素的 CSS 类。

| CSS 类                       | 指示&hellip; |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | 断开连接。 客户端正在尝试重新连接。 显示模式。 |
| `components-reconnect-hide`     | 将为服务器重新建立活动连接。 隐藏模式。 |
| `components-reconnect-failed`   | 重新连接失败，可能是由于网络故障引起的。 若要尝试重新连接，请调用 `window.Blazor.reconnect()`。 |
| `components-reconnect-rejected` | 已拒绝重新连接。 服务器已达到但拒绝了连接，并且服务器上的用户状态丢失。 若要重新加载应用，请调用 `location.reload()`。 当以下情况时，可能会导致此连接状态：<ul><li>服务器端线路发生崩溃。</li><li>断开客户端的连接时间足以使服务器删除用户的状态。 将释放用户与之交互的组件的实例。</li><li>服务器已重新启动，或者应用程序的工作进程被回收。</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>预呈现后有状态重新连接

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

当 `ServerPrerendered``RenderMode` 时，组件最初作为页面的一部分以静态方式呈现。 一旦浏览器与服务器建立了连接，该组件将*再次*呈现，并且该组件现在是交互式的。 如果存在用于初始化组件的[OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods)生命周期方法，则将执行*两次*此方法：

* 如果组件预呈现静态，则为。
* 建立服务器连接之后。

这可能会导致在最终呈现组件时，UI 中显示的数据发生显著变化。

若要避免 Blazor 服务器应用中出现双重渲染方案：

* 传入一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重新启动后检索状态。
* 在预呈现期间使用标识符以保存组件状态。
* 在预呈现后使用标识符检索缓存状态。

下面的代码演示基于模板的 Blazor 服务器应用中的更新 `WeatherForecastService`，可避免双重呈现：

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

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

### <a name="detect-when-the-app-is-prerendering"></a>检测预呈现应用的时间

[!INCLUDE[](~/includes/blazor-prerendering.md)]

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

## <a name="additional-resources"></a>其他资源

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:tutorials/signalr-blazor-webassembly>
