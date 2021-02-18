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
ms.openlocfilehash: 94b5a3d8c0f5b94ecac32e6fc5f94efeb8337f37
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280352"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="bb2ac-103">ASP.NET Core Blazor 项目结构</span><span class="sxs-lookup"><span data-stu-id="bb2ac-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="bb2ac-104">本文介绍了 Blazor 项目模板生成的 Blazor 应用所包含的文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-104">This article describes the files and folders that make up a Blazor app generated from the Blazor project templates.</span></span>

## Blazor WebAssembly

<span data-ttu-id="bb2ac-105">Blazor WebAssembly 模板 (`blazorwasm`) 创建 Blazor WebAssembly 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-105">The Blazor WebAssembly template (`blazorwasm`) creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="bb2ac-106">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从静态资产 `weather.json` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-106">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="bb2ac-107">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-107">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="bb2ac-108">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-108">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="bb2ac-109">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-109">The template includes the following components:</span></span>
  * <span data-ttu-id="bb2ac-110">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-110">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="bb2ac-111">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-111">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="bb2ac-112">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-112">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="bb2ac-113">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-113">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="bb2ac-114">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-114">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="bb2ac-115">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-115">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="bb2ac-116">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-116">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="bb2ac-117">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-117">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="bb2ac-118">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-118">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="bb2ac-119"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-119">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="bb2ac-120">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-120">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="bb2ac-121">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-121">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="bb2ac-122">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-122">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="bb2ac-123">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-123">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="bb2ac-124">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-124">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="bb2ac-125">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-125">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="bb2ac-126"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-126">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="bb2ac-127">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-127">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="bb2ac-128">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-128">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="bb2ac-129">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-129">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="bb2ac-130">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-130">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="bb2ac-131">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-131">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="bb2ac-132">使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-132">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="bb2ac-133">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-133">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="bb2ac-134">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-134">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="bb2ac-135">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-135">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="bb2ac-136">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-136">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="bb2ac-137">组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-137">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="bb2ac-138">添加到 `wwwroot/index.html` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-138">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="bb2ac-139">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-139">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="bb2ac-140">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-140">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="bb2ac-141">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-141">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="bb2ac-142">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-142">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="bb2ac-143"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-143">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="bb2ac-144">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-144">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="bb2ac-145">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-145">The `App` component is the root component of the app.</span></span> <span data-ttu-id="bb2ac-146">对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-146">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="bb2ac-147">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-147">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="bb2ac-148">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-148">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="bb2ac-149">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-149">The `App` component is the root component of the app.</span></span> <span data-ttu-id="bb2ac-150">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-150">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="bb2ac-151">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-151">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="bb2ac-152">Blazor Server 模板 (`blazorserver`) 创建 Blazor Server 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-152">The Blazor Server template (`blazorserver`) creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="bb2ac-153">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从注册服务 `WeatherForecastService` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-153">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="bb2ac-154">`Data` 文件夹：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-154">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="bb2ac-155">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-155">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="bb2ac-156">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-156">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="bb2ac-157">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-157">The template includes the following:</span></span>
  * <span data-ttu-id="bb2ac-158">`_Host.cshtml`：实现为 Razor 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-158">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="bb2ac-159">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-159">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="bb2ac-160">此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-160">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="bb2ac-161">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-161">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="bb2ac-162">`Error` 组件 (`Error.razor`)：当应用中发生未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-162">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="bb2ac-163">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-163">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="bb2ac-164">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-164">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="bb2ac-165">添加到 `Pages/_Host.cshtml` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-165">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="bb2ac-166">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-166">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="bb2ac-167">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-167">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="bb2ac-168">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-168">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="bb2ac-169">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-169">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="bb2ac-170">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-170">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="bb2ac-171">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-171">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="bb2ac-172">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-172">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="bb2ac-173">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-173">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="bb2ac-174"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-174">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="bb2ac-175">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-175">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="bb2ac-176">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-176">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="bb2ac-177">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-177">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="bb2ac-178">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-178">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="bb2ac-179">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-179">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="bb2ac-180">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-180">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="bb2ac-181"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-181">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="bb2ac-182">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-182">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="bb2ac-183">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-183">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="bb2ac-184">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-184">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="bb2ac-185">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-185">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="bb2ac-186"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-186">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="bb2ac-187">`appsettings.json` 和环境应用设置文件：提供应用的[配置设置](xref:blazor/fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-187">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="bb2ac-188">`Program.cs`：应用的入口点，用于设置 ASP.NET Core [主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-188">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="bb2ac-189">`Startup.cs`：包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-189">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="bb2ac-190">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-190">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="bb2ac-191">`ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-191">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="bb2ac-192">通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-192">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="bb2ac-193">`Configure`：配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="bb2ac-193">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="bb2ac-194">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-194"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="bb2ac-195">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-195">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="bb2ac-196">调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="bb2ac-196">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>
