---
title: ASP.NET Core Blazor 模板
author: guardrex
description: 了解 ASP.NET Core Blazor 应用模板和 Blazor 项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649446"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core Blazor 模板

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型开发应用：

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。

有关基于模板创建 Blazor 应用的分步说明，请参阅 <xref:blazor/get-started>。

## <a name="opno-locblazor-project-structure"></a>Blazor 项目结构

以下文件和文件夹构成了基于 Blazor 模板生成的 Blazor 应用：

* *Program.cs*&ndash; 应用的入口点，用于设置以下各项：

  * ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly 主机 (Blazor WebAssembly) &ndash; 该文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。
    * 作为应用根组件的 `App` 组件被指定为 `Add` 方法的 `app` DOM 元素。
    * 可以在主机生成器上使用 `ConfigureServices` 方法配置服务（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>();`）。
    * 可以通过主机生成器提供配置 (`builder.Configuration`)。

* *Startup.cs* (Blazor Server) &ndash; 包含应用的启动逻辑。 `Startup` 类定义两个方法：

  * `ConfigureServices`&ndash; 配置应用的 [ 依赖项注入 (DI)](xref:fundamentals/dependency-injection) 服务。 在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。
  * `Configure`&ndash; 配置应用的请求处理管道：
    * 调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> 可以为与浏览器的实时连接设置终结点。 使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。
    * 调用 [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 可以设置应用的根页面 (*Pages/_Host.cshtml*) 并启用导航。

* *wwwroot/index.html* (Blazor WebAssembly) &ndash; 实现为 HTML 页面的应用根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 `App` 组件 (*App.razor*) 在 `Startup.Configure` 中指定为 `AddComponent` 方法的 `app` DOM 元素。
  * 加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：
    * 下载 .NET 运行时、应用和应用依赖项。
    * 初始化运行时以运行应用。

* *Pages/_Host.cshtml* (Blazor Server) &ndash; 实现为 Razor 页面的应用根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。
  * “主机”页面指定根 `App` 组件 (*App.razor*) 的呈现位置。

* *App.razor*&ndash; 应用的根组件，它使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件设置客户端路由。 `Router` 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

* *Pages* 文件夹 &ndash; 包含构成 Blazor 应用的可路由组件/页面 ( *.razor*)。 每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。 该模板包括以下组件：
  * `Index` (*Index.razor*) &ndash; 实现主页。
  * `Counter` (*Counter.razor*) &ndash; 实现“计数器”页面。
  * `Error`（*Error.razor*，仅限 Blazor Server 应用）&ndash; 当应用中出现未经处理的异常时呈现。
  * `FetchData` (*FetchData.razor*) &ndash; 实现“提取数据”页面。

* *Shared* 文件夹 &ndash; 包含应用使用的其他 UI 组件 ( *.razor*)：
  * `MainLayout` (*MainLayout.razor*) &ndash; 应用的布局组件。
  * `NavMenu` (*NavMenu.razor*) &ndash; 实现侧栏导航。 包括 [NavLink 组件](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 `NavLink` 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。

* *_Imports.razor*&ndash; 包括要包含在应用组件 ( *.razor*) 中的常见 Razor 指令，例如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。

* *Data* 文件夹 (Blazor Server) &ndash; 包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。

* *wwwroot*&ndash; 应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。

* *appsettings.json* (Blazor Server) &ndash; 应用的配置设置。
