---
title: ASP.NET Core Blazor 项目结构
author: guardrex
description: 了解 ASP.NET Core Blazor 应用项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97513530"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor 项目结构

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

以下文件和文件夹构成了基于 Blazor 项目模板生成的 Blazor 应用：

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`：应用入口点，用于设置以下各项：

  * ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。
    * `App` 组件是应用的根组件。 对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。
    * 添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`：应用入口点，用于设置以下各项：

  * ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。
    * `App` 组件是应用的根组件。 `App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。
    * 添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。

::: moniker-end

* `Startup.cs` (Blazor Server)：包含应用的启动逻辑。 `Startup` 类定义两个方法：

  * `ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。 在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。
  * `Configure`：配置应用的请求处理管道：
    * 调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。 使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。
    * 调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。

::: moniker range=">= aspnetcore-5.0"

* `wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。
  * 加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：
    * 下载 .NET 运行时、应用和应用依赖项。
    * 初始化运行时以运行应用。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。
  * 加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：
    * 下载 .NET 运行时、应用和应用依赖项。
    * 初始化运行时以运行应用。

::: moniker-end

* `App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

* `Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。 每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。 该模板包括以下组件：
  * `_Host.cshtml` (Blazor Server)：实现为 Razor 页面的应用的根页面：
    * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
    * 加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。
    * 此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。
  * `Counter` 组件 (`Pages/Counter.razor`)：实现“计数器”页面。
  * `Error` 组件（`Error.razor`，仅限 Blazor Server应用）：当应用中发生未经处理的异常时呈现。
  * `FetchData` 组件 (`Pages/FetchData.razor`)：实现“提取数据”页面。
  * `Index` 组件 (`Pages/Index.razor`)：实现主页。
  
* `Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。

::: moniker range=">= aspnetcore-5.0"

* `Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `MainLayout.razor.css`：应用主布局的样式表。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  * `NavMenu.razor.css`：应用导航菜单的样式表。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  
::: moniker-end

* `_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。

* `Data` 文件夹 (Blazor Server)：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。

* `wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。

* `appsettings.json`：保留应用的[配置设置](xref:blazor/fundamentals/configuration)。 在 Blazor WebAssembly 应用中，应用设置文件位于 `wwwroot` 文件夹中。 在 Blazor Server 应用中，应用设置文件位于项目根目录中。
