---
title: ASP.NET Core Blazor 宿主模型
author: guardrex
description: 了解 Blazor 客户端和服务器端的托管模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/hosting-models
ms.openlocfilehash: f7a16d64e1f874a4f6b3c8db5217810b13c7c6ff
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800425"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 宿主模型

作者： [Daniel Roth](https://github.com/danroth27)

Blazor 是一种 web 框架，旨在在基于[WebAssembly](https://webassembly.org/)的 .net 运行时（*Blazor 客户*端）上的浏览器中运行客户端，或在 ASP.NET Core （*Blazor 服务器端*）中使用服务器端。 无论采用何种托管模型，应用和组件模型*都是相同*的。

若要为本文中所述的宿主模型创建项目，请<xref:blazor/get-started>参阅。

## <a name="client-side"></a>客户端

Blazor 的主体托管模型在 WebAssembly 上的浏览器中运行客户端。 将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。 应用将在浏览器线程中直接执行。 UI 更新和事件处理发生在同一进程中。 应用的资产将作为静态文件部署到 web 服务器或可为客户端提供静态内容的服务。

![Blazor 客户端：Blazor 应用程序在浏览器中的 UI 线程上运行。](hosting-models/_static/client-side.png)

若要使用客户端托管模型创建 Blazor 应用，请使用**Blazor WebAssembly 应用**模板（[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)）。

选择**Blazor WebAssembly 应用程序**模板之后，你可以选择将应用配置为使用 ASP.NET Core 后端，方法是选择 " **ASP.NET Core 托管**" 复选框（"[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)"）。 ASP.NET Core 应用程序将 Blazor 应用程序提供给客户端。 Blazor 客户端应用可通过网络使用 web API 调用或[SignalR](xref:signalr/introduction)与服务器交互。

这些模板包含处理以下内容的*blazor. webassembly*脚本：

* 下载 .NET 运行时、应用程序和应用程序的依赖项。
* 用于运行应用程序的运行时初始化。

客户端托管模型具有以下几个优点：

* 没有 .NET 服务器端依赖项。 应用在下载到客户端之后完全正常运行。
* 完全利用客户端资源和功能。
* 工作从服务器卸载到客户端。
* 不需要 ASP.NET Core web 服务器来托管应用程序。 无服务器部署方案可能（例如，通过 CDN 提供应用）。

客户端托管有缺点：

* 应用程序限制为浏览器的功能。
* 需要支持的客户端硬件和软件（例如，WebAssembly 支持）。
* 下载大小较大，应用需要较长时间才能加载。
* .NET 运行时和工具支持不太成熟。 例如， [.NET Standard](/dotnet/standard/net-standard)支持和调试中存在限制。

## <a name="server-side"></a>服务器端

使用服务器端承载模型，可在服务器上从 ASP.NET Core 的应用程序中执行应用。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

![浏览器通过 SignalR 连接与服务器上 ASP.NET Core 应用程序内托管的应用程序进行交互。](hosting-models/_static/server-side.png)

若要使用服务器端承载模型创建 Blazor 应用，请使用 ASP.NET Core **Blazor 服务器应用**模板（[dotnet new blazorserver](/dotnet/core/tools/dotnet-new)）。 ASP.NET Core 应用托管服务器端应用，并创建客户端连接到的 SignalR 终结点。

ASP.NET Core 应用引用要添加的应用`Startup`的类：

* 服务器端服务。
* 请求处理管道的应用。

*Blazor*脚本&dagger;建立客户端连接。 应用负责根据需要保存和还原应用状态（例如，在网络连接丢失的情况下）。

服务器端托管模型具有以下几个优点：

* 下载大小明显小于客户端应用，且应用加载速度更快。
* 应用充分利用服务器功能，包括使用任何与 .NET Core 兼容的 Api。
* 服务器上的 .NET Core 用于运行应用程序，因此现有的 .NET 工具（如调试）可按预期方式工作。
* 支持瘦客户端。 例如，服务器端应用程序适用于不支持 WebAssembly 的浏览器和资源限制的设备。
* 应用程序的 .NET/C#代码库（包括应用程序的组件代码）不会提供给客户端。

服务器端托管有缺点：

* 通常存在较高的延迟。 每个用户交互都涉及网络跃点。
* 无脱机支持。 如果客户端连接失败，应用将停止工作。
* 对于包含多个用户的应用而言，可伸缩性非常困难。 服务器必须管理多个客户端连接并处理客户端状态。
* 为应用提供服务需要 ASP.NET Core 服务器。 不可能的无服务器部署方案（例如，通过 CDN 为应用提供服务）。

&dagger;*Blazor*脚本是从 ASP.NET Core 共享框架中的嵌入资源提供的。

### <a name="reconnection-to-the-same-server"></a>重新连接到同一台服务器

Blazor 服务器端应用需要与服务器建立活动的 SignalR 连接。 如果连接丢失，应用会尝试重新连接到服务器。 只要客户端的状态仍在内存中，客户端会话便会恢复而不会失去状态。
 
当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。 如果重新连接失败，则会向用户提供重试选项。 若要自定义 UI，请在`components-reconnect-modal` *_Host* Razor 页`id`中使用作为其定义的元素。 客户端根据连接状态将此元素更新为下面的一个 CSS 类：
 
* `components-reconnect-show`&ndash;显示 UI 以指示连接已丢失，并且客户端正在尝试重新连接。
* `components-reconnect-hide`&ndash;客户端具有活动的连接，隐藏 UI。
* `components-reconnect-failed`&ndash;重新连接失败。 若要再次尝试重新连接`window.Blazor.reconnect()`，请调用。

### <a name="stateful-reconnection-after-prerendering"></a>预呈现后有状态重新连接
 
默认情况下，Blazor 服务器端应用设置为在客户端与服务器建立连接之前，在服务器上预呈现 UI。 这是在 *_Host* Razor 页面中设置的：
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`配置组件是否：

* 已预呈现到页面中。
* 在页面上呈现为静态 HTML，或者，如果包含从用户代理启动 Blazor 应用所需的信息，则为。

| `RenderMode`        | 描述 |
| ------------------- | ----------- |
| `ServerPrerendered` | 将组件呈现为静态 HTML，并包含 Blazor 服务器端应用程序的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 不支持参数。 |
| `Server`            | 呈现 Blazor 服务器端应用程序的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 不支持参数。 |
| `Static`            | 将组件呈现为静态 HTML。 支持参数。 |

不支持从静态 HTML 页面呈现服务器组件。
 
客户端重新连接到服务器，该服务器具有用于预呈现应用的相同状态。 如果应用的状态仍在内存中，则在建立 SignalR 连接后，组件状态将不重新呈现。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>从 Razor 页面和视图呈现有状态交互式组件
 
可以将有状态交互组件添加到 Razor 页面或视图。

呈现页面或视图时：

* 该组件与页面或视图预呈现。
* 用于预呈现的初始组件状态将丢失。
* 建立 SignalR 连接时，将创建新的组件状态。
 
以下 Razor 页面呈现`Counter`组件：

```cshtml
<h1>My Razor Page</h1>
 
@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>从 Razor 页面和视图呈现非交互式组件

在以下 Razor 页面中， `MyComponent`组件以静态方式呈现，其初始值是使用窗体指定的：
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>
 
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

由于`MyComponent`是静态呈现的，因此该组件不能是交互式的。

### <a name="detect-when-the-app-is-prerendering"></a>检测预呈现应用的时间
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>为 Blazor 服务器端应用配置 SignalR 客户端
 
有时，你需要配置 Blazor 服务器端应用使用的 SignalR 客户端。 例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。
 
若要在*Pages/_Host*文件中配置 SignalR 客户端：

* 将属性添加到 blazor `<script>`脚本的标记中。 `autostart="false"`
* 调用`Blazor.start`并传入指定 SignalR 生成器的配置对象。
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>其他资源

* <xref:blazor/get-started>
* <xref:signalr/introduction>
