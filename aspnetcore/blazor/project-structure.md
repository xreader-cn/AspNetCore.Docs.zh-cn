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
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513530"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="aeddb-103">ASP.NET Core Blazor 项目结构</span><span class="sxs-lookup"><span data-stu-id="aeddb-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="aeddb-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aeddb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aeddb-105">以下文件和文件夹构成了基于 Blazor 项目模板生成的 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="aeddb-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="aeddb-106">`Program.cs`：应用入口点，用于设置以下各项：</span><span class="sxs-lookup"><span data-stu-id="aeddb-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="aeddb-107">ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="aeddb-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="aeddb-108">WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。</span><span class="sxs-lookup"><span data-stu-id="aeddb-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="aeddb-109">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="aeddb-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="aeddb-110">对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="aeddb-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="aeddb-111">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="aeddb-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="aeddb-112">`Program.cs`：应用入口点，用于设置以下各项：</span><span class="sxs-lookup"><span data-stu-id="aeddb-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="aeddb-113">ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="aeddb-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="aeddb-114">WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。</span><span class="sxs-lookup"><span data-stu-id="aeddb-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="aeddb-115">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="aeddb-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="aeddb-116">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。</span><span class="sxs-lookup"><span data-stu-id="aeddb-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="aeddb-117">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="aeddb-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="aeddb-118">`Startup.cs` (Blazor Server)：包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="aeddb-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="aeddb-119">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="aeddb-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="aeddb-120">`ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="aeddb-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="aeddb-121">在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="aeddb-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="aeddb-122">`Configure`：配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="aeddb-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="aeddb-123">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="aeddb-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="aeddb-124">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="aeddb-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="aeddb-125">调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="aeddb-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="aeddb-126">`wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="aeddb-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="aeddb-127">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="aeddb-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="aeddb-128">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="aeddb-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="aeddb-129">使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。</span><span class="sxs-lookup"><span data-stu-id="aeddb-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="aeddb-130">加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：</span><span class="sxs-lookup"><span data-stu-id="aeddb-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="aeddb-131">下载 .NET 运行时、应用和应用依赖项。</span><span class="sxs-lookup"><span data-stu-id="aeddb-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="aeddb-132">初始化运行时以运行应用。</span><span class="sxs-lookup"><span data-stu-id="aeddb-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="aeddb-133">`wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="aeddb-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="aeddb-134">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="aeddb-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="aeddb-135">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="aeddb-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="aeddb-136">组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。</span><span class="sxs-lookup"><span data-stu-id="aeddb-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="aeddb-137">加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：</span><span class="sxs-lookup"><span data-stu-id="aeddb-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="aeddb-138">下载 .NET 运行时、应用和应用依赖项。</span><span class="sxs-lookup"><span data-stu-id="aeddb-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="aeddb-139">初始化运行时以运行应用。</span><span class="sxs-lookup"><span data-stu-id="aeddb-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="aeddb-140">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="aeddb-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="aeddb-141"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="aeddb-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="aeddb-142">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="aeddb-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="aeddb-143">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="aeddb-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="aeddb-144">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="aeddb-144">The template includes the following:</span></span>
  * <span data-ttu-id="aeddb-145">`_Host.cshtml` (Blazor Server)：实现为 Razor 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="aeddb-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="aeddb-146">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="aeddb-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="aeddb-147">加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="aeddb-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="aeddb-148">此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="aeddb-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="aeddb-149">`Counter` 组件 (`Pages/Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="aeddb-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="aeddb-150">`Error` 组件（`Error.razor`，仅限 Blazor Server应用）：当应用中发生未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="aeddb-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="aeddb-151">`FetchData` 组件 (`Pages/FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="aeddb-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="aeddb-152">`Index` 组件 (`Pages/Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="aeddb-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="aeddb-153">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="aeddb-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="aeddb-154">`Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：</span><span class="sxs-lookup"><span data-stu-id="aeddb-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="aeddb-155">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="aeddb-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="aeddb-156">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="aeddb-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="aeddb-157">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="aeddb-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="aeddb-158">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="aeddb-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="aeddb-159"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="aeddb-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="aeddb-160">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="aeddb-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="aeddb-161">`Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：</span><span class="sxs-lookup"><span data-stu-id="aeddb-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="aeddb-162">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="aeddb-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="aeddb-163">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="aeddb-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="aeddb-164">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="aeddb-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="aeddb-165"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="aeddb-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="aeddb-166">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="aeddb-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="aeddb-167">`Data` 文件夹 (Blazor Server)：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="aeddb-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="aeddb-168">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="aeddb-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="aeddb-169">`appsettings.json`：保留应用的[配置设置](xref:blazor/fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="aeddb-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="aeddb-170">在 Blazor WebAssembly 应用中，应用设置文件位于 `wwwroot` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="aeddb-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="aeddb-171">在 Blazor Server 应用中，应用设置文件位于项目根目录中。</span><span class="sxs-lookup"><span data-stu-id="aeddb-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
