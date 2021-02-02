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
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="0962d-103">ASP.NET Core Blazor 项目结构</span><span class="sxs-lookup"><span data-stu-id="0962d-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="0962d-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0962d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0962d-105">本文介绍了 Blazor 项目模板生成的 Blazor 应用所包含的文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="0962d-105">This article describes the files and folders that make up a Blazor app generated from the Blazor project templates.</span></span>

## Blazor WebAssembly

<span data-ttu-id="0962d-106">Blazor WebAssembly 模板 (`blazorwasm`) 创建 Blazor WebAssembly 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="0962d-106">The Blazor WebAssembly template (`blazorwasm`) creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="0962d-107">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从静态资产 `weather.json` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="0962d-107">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="0962d-108">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`)。</span><span class="sxs-lookup"><span data-stu-id="0962d-108">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="0962d-109">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="0962d-109">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0962d-110">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="0962d-110">The template includes the following components:</span></span>
  * <span data-ttu-id="0962d-111">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-111">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="0962d-112">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-112">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="0962d-113">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="0962d-113">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="0962d-114">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="0962d-114">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0962d-115">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="0962d-115">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="0962d-116">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="0962d-116">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="0962d-117">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="0962d-117">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="0962d-118">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="0962d-118">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="0962d-119">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="0962d-119">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="0962d-120"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-120">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="0962d-121">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="0962d-121">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="0962d-122">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-122">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0962d-123">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="0962d-123">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="0962d-124">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="0962d-124">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="0962d-125">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="0962d-125">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="0962d-126">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="0962d-126">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="0962d-127"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-127">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="0962d-128">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-128">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0962d-129">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="0962d-129">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="0962d-130">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="0962d-130">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="0962d-131">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="0962d-131">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="0962d-132">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="0962d-132">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="0962d-133">使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-133">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0962d-134">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="0962d-134">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="0962d-135">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="0962d-135">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="0962d-136">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="0962d-136">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="0962d-137">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="0962d-137">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="0962d-138">组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。</span><span class="sxs-lookup"><span data-stu-id="0962d-138">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0962d-139">添加到 `wwwroot/index.html` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="0962d-139">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="0962d-140">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="0962d-140">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="0962d-141">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="0962d-141">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="0962d-142">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="0962d-142">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="0962d-143">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="0962d-143">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="0962d-144"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-144">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0962d-145">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="0962d-145">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="0962d-146">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-146">The `App` component is the root component of the app.</span></span> <span data-ttu-id="0962d-147">对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="0962d-147">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="0962d-148">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="0962d-148">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0962d-149">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="0962d-149">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="0962d-150">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-150">The `App` component is the root component of the app.</span></span> <span data-ttu-id="0962d-151">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。</span><span class="sxs-lookup"><span data-stu-id="0962d-151">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="0962d-152">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="0962d-152">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="0962d-153">Blazor Server 模板 (`blazorserver`) 创建 Blazor Server 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="0962d-153">The Blazor Server template (`blazorserver`) creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="0962d-154">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从注册服务 `WeatherForecastService` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="0962d-154">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="0962d-155">`Data` 文件夹：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="0962d-155">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="0962d-156">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-156">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="0962d-157">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="0962d-157">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0962d-158">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="0962d-158">The template includes the following:</span></span>
  * <span data-ttu-id="0962d-159">`_Host.cshtml`：实现为 Razor 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="0962d-159">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="0962d-160">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="0962d-160">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="0962d-161">此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="0962d-161">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="0962d-162">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-162">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="0962d-163">`Error` 组件 (`Error.razor`)：当应用中发生未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="0962d-163">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="0962d-164">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-164">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="0962d-165">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="0962d-165">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="0962d-166">添加到 `Pages/_Host.cshtml` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="0962d-166">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="0962d-167">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="0962d-167">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="0962d-168">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="0962d-168">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="0962d-169">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="0962d-169">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0962d-170">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="0962d-170">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="0962d-171">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="0962d-171">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="0962d-172">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="0962d-172">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="0962d-173">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="0962d-173">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="0962d-174">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="0962d-174">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="0962d-175"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-175">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="0962d-176">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="0962d-176">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="0962d-177">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-177">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0962d-178">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="0962d-178">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="0962d-179">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="0962d-179">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="0962d-180">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="0962d-180">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="0962d-181">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="0962d-181">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="0962d-182"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-182">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="0962d-183">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="0962d-183">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="0962d-184">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="0962d-184">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="0962d-185">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="0962d-185">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="0962d-186">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="0962d-186">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="0962d-187"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="0962d-187">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="0962d-188">`appsettings.json` 和环境应用设置文件：提供应用的[配置设置](xref:blazor/fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="0962d-188">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="0962d-189">`Program.cs`：应用的入口点，用于设置 ASP.NET Core [主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="0962d-189">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="0962d-190">`Startup.cs`：包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="0962d-190">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="0962d-191">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="0962d-191">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="0962d-192">`ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="0962d-192">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="0962d-193">通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="0962d-193">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="0962d-194">`Configure`：配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="0962d-194">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="0962d-195">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="0962d-195"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="0962d-196">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="0962d-196">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="0962d-197">调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="0962d-197">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>
