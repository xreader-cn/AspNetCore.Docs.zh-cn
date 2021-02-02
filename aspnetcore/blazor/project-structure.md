---
title: ASP.NET Core Blazor 项目结构
author: guardrex
description: 了解 ASP.NET Core Blazor 应用项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751537"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor 项目结构

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

本文介绍了 Blazor 项目模板生成的 Blazor 应用所包含的文件和文件夹。

## Blazor WebAssembly

Blazor WebAssembly 模板 (`blazorwasm`) 创建 Blazor WebAssembly 应用的初始文件和目录结构。 该应用中填充了一个 `FetchData` 组件的演示代码，该组件从静态资产 `weather.json` 加载数据，且用户与 `Counter` 组件交互。

* `Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`)。 每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。 该模板包括以下组件：
  * `Counter` 组件 (`Counter.razor`)：实现“计数器”页面。
  * `FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。
  * `Index` 组件 (`Index.razor`)：实现主页。
  
* `Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。

::: moniker range=">= aspnetcore-5.0"

* `Shared` 文件夹：包含以下共享组件和样式表：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `MainLayout.razor.css`：应用主布局的样式表。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  * `NavMenu.razor.css`：应用导航菜单的样式表。
  * `SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 文件夹：包含以下共享组件：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  * `SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。 `index.html` 网页是实现为 HTML 页面的应用的根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。 `index.html` 网页是实现为 HTML 页面的应用的根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。

::: moniker-end

> [!NOTE]
> 添加到 `wwwroot/index.html` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。 在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。 例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。

* `_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。

* `App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`：应用入口点，用于设置 WebAssembly 主机：
  
  * `App` 组件是应用的根组件。 对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。
  * 添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`：应用入口点，用于设置 WebAssembly 主机：

  * `App` 组件是应用的根组件。 `App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。
  * 添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。

::: moniker-end

## Blazor Server

Blazor Server 模板 (`blazorserver`) 创建 Blazor Server 应用的初始文件和目录结构。 该应用中填充了一个 `FetchData` 组件的演示代码，该组件从注册服务 `WeatherForecastService` 加载数据，且用户与 `Counter` 组件交互。

* `Data` 文件夹：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。

* `Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。 每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。 该模板包括以下组件：
  * `_Host.cshtml`：实现为 Razor 页面的应用的根页面：
    * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
    * 此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。
  * `Counter` 组件 (`Counter.razor`)：实现“计数器”页面。
  * `Error` 组件 (`Error.razor`)：当应用中发生未经处理的异常时呈现。
  * `FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。
  * `Index` 组件 (`Index.razor`)：实现主页。

> [!NOTE]
> 添加到 `Pages/_Host.cshtml` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。 在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。 例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。

* `Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。

::: moniker range=">= aspnetcore-5.0"

* `Shared` 文件夹：包含以下共享组件和样式表：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `MainLayout.razor.css`：应用主布局的样式表。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  * `NavMenu.razor.css`：应用导航菜单的样式表。
  * `SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 文件夹：包含以下共享组件：
  * `MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。
  * `NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。
  * `SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。
  
::: moniker-end

* `wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。

* `_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。

* `App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

* `appsettings.json` 和环境应用设置文件：提供应用的[配置设置](xref:blazor/fundamentals/configuration)。

* `Program.cs`：应用的入口点，用于设置 ASP.NET Core [主机](xref:fundamentals/host/generic-host)。

* `Startup.cs`：包含应用的启动逻辑。 `Startup` 类定义两个方法：

  * `ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。 通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。
  * `Configure`：配置应用的请求处理管道：
    * 调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。 使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。
    * 调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。
