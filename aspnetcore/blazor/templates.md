---
title: ASP.NET Core Blazor 模板
author: guardrex
description: 了解 ASP.NET Core Blazor 应用程序模板和 Blazor 项目结构。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: bc0ea4a777e8684a7b0925377b8a19a45c2b531c
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879656"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core Blazor 模板

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 框架提供模板，用于为每个 Blazor 承载模型开发应用程序：

* Blazor WebAssembly （`blazorwasm`）
* Blazor 服务器（`blazorserver`）

有关 Blazor的宿主模型的详细信息，请参阅 <xref:blazor/hosting-models>。

有关基于模板创建 Blazor 应用程序的分步说明，请参阅 <xref:blazor/get-started>。

## <a name="opno-locblazor-project-structure"></a>Blazor 项目结构

以下文件和文件夹构成了 Blazor 模板生成的 Blazor 应用：

* *Program.cs* &ndash; 设置 ASP.NET Core[主机](xref:fundamentals/host/generic-host)的应用入口点。 此文件中的代码对于从 ASP.NET Core 模板生成的所有 ASP.NET Core 应用都是通用的。

* *Startup.cs* &ndash; 包含应用的启动逻辑。 `Startup` 类定义两种方法：

  * `ConfigureServices` &ndash; 配置应用的[依赖项注入（DI）](xref:fundamentals/dependency-injection)服务。 在 Blazor Server apps 中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>添加服务，`WeatherForecastService` 将添加到服务容器中，供示例 `FetchData` 组件使用。
  * `Configure` &ndash; 配置应用的请求处理管道：
    * Blazor WebAssembly &ndash; 将 `App` 组件（指定为 `app` DOM 元素添加到 `AddComponent` 方法），这是应用的根组件。
    * Blazor 服务器
      * 调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> 来设置与浏览器进行实时连接时使用的终结点。 该连接是使用[SignalR](xref:signalr/introduction)创建的，它是一个框架，用于向应用程序添加实时 web 功能。
      * 调用[MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)以设置应用的根页面（*Pages/_Host*），并启用导航。

* *wwwroot/index.html* （Blazor WebAssembly） &ndash; 作为 html 页面实现的应用的根页面：
  * 当最初请求应用的任何页面时，此页将呈现并在响应中返回。
  * 页面指定 `App` 呈现根组件的位置。 `App` 组件（*app.config*）指定为 `Startup.Configure`中 `AddComponent` 方法的 `app` DOM 元素。
  * 已加载 *_framework/Blazor.webassembly.js* JavaScript 文件，该文件：
    * 下载 .NET 运行时、应用程序和应用程序的依赖项。
    * 初始化运行时以运行应用程序。

* *Pages/_Host cshtml* （Blazor Server） &ndash; 作为 Razor 页面实现的应用的根页面：
  * 当最初请求应用的任何页面时，此页将呈现并在响应中返回。
  * 加载 *_framework/Blazor.server.js* JavaScript 文件，该文件将设置浏览器与服务器之间的实时 SignalR 连接。
  * "主机" 页指定根 `App` 组件（*app.config*）的呈现位置。

* *App.config* &ndash; 使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件设置客户端路由的应用程序的根组件。 `Router` 组件会截获浏览器导航，并呈现与请求的地址匹配的页面。

* *Pages*文件夹 &ndash; 包含组成 Blazor 应用的可路由组件/页面（*razor*）。 每页的路由都是使用[`@page`](xref:mvc/views/razor#page)指令指定的。 此模板包括以下组件：
  * `Index` （&ndash;*razor*）实现主页。
  * `Counter` （*razor*） &ndash; 实现计数器页。
  * 应用中发生未经处理的异常时，将 `Error` （仅限*Error*、Blazor Server app） &ndash; 呈现。
  * `FetchData` （*FetchData*） &ndash; 实现 "提取数据" 页。

* *共享*文件夹 &ndash; 包含应用使用的其他 UI 组件（*razor*）：
  * `MainLayout` （*MainLayout*） &ndash; 应用的布局组件。
  * `NavMenu` （*NavMenu*） &ndash; 实现侧栏导航。 包含[NavLink 组件](xref:blazor/routing#navlink-component)（<xref:Microsoft.AspNetCore.Components.Routing.NavLink>），该组件将呈现指向其他 Razor 组件的导航链接。 加载组件时，`NavLink` 组件会自动指示选定状态，这有助于用户了解当前显示的组件。

* *_Imports* &ndash; 包含包含在应用组件（*razor*）中的常见 razor 指令，如用于命名空间的[`@using`](xref:mvc/views/razor#using)指令。

* *数据*文件夹（Blazor Server） &ndash; 包含向应用程序的 `FetchData` 组件提供示例天气数据的 `WeatherForecastService` 的 `WeatherForecast` 类和实现。

* *wwwroot* &ndash; 包含应用的公共静态资产的应用的[Web 根](xref:fundamentals/index#web-root)文件夹。

* *appsettings* （Blazor Server） &ndash; 应用的配置设置。
