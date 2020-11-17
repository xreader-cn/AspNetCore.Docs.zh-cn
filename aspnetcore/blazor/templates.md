---
title: ASP.NET Core Blazor 模板
author: guardrex
description: 了解 ASP.NET Core Blazor 应用模板和 Blazor 项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: fc2e81cf130732d515fb871227031493e297cf9f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507767"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="1a24a-103">ASP.NET Core Blazor 模板</span><span class="sxs-lookup"><span data-stu-id="1a24a-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="1a24a-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a24a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1a24a-105">Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型开发应用：</span><span class="sxs-lookup"><span data-stu-id="1a24a-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="1a24a-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="1a24a-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="1a24a-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="1a24a-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="1a24a-108">有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="1a24a-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="1a24a-109">通过将 `--help` 选项传递给 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 命令，可提供模板选项：</span><span class="sxs-lookup"><span data-stu-id="1a24a-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="1a24a-110">Blazor 项目结构</span><span class="sxs-lookup"><span data-stu-id="1a24a-110">Blazor project structure</span></span>

<span data-ttu-id="1a24a-111">以下文件和文件夹构成了基于 Blazor 项目模板生成的 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="1a24a-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="1a24a-112">`Program.cs`：应用入口点，用于设置以下各项：</span><span class="sxs-lookup"><span data-stu-id="1a24a-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="1a24a-113">ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="1a24a-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="1a24a-114">WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。</span><span class="sxs-lookup"><span data-stu-id="1a24a-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="1a24a-115">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="1a24a-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="1a24a-116">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("#app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）。</span><span class="sxs-lookup"><span data-stu-id="1a24a-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="1a24a-117">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="1a24a-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="1a24a-118">`Program.cs`：应用入口点，用于设置以下各项：</span><span class="sxs-lookup"><span data-stu-id="1a24a-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="1a24a-119">ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="1a24a-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="1a24a-120">WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。</span><span class="sxs-lookup"><span data-stu-id="1a24a-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="1a24a-121">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="1a24a-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="1a24a-122">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。</span><span class="sxs-lookup"><span data-stu-id="1a24a-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="1a24a-123">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="1a24a-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="1a24a-124">`Startup.cs` (Blazor Server)：包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="1a24a-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="1a24a-125">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="1a24a-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="1a24a-126">`ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="1a24a-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="1a24a-127">在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="1a24a-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="1a24a-128">`Configure`：配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="1a24a-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="1a24a-129">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="1a24a-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="1a24a-130">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="1a24a-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="1a24a-131">调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="1a24a-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="1a24a-132">`wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="1a24a-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="1a24a-133">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="1a24a-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="1a24a-134">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="1a24a-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="1a24a-135">组件呈现在 `app` DOM 元素 (`<app>...</app>`) 的位置。</span><span class="sxs-lookup"><span data-stu-id="1a24a-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="1a24a-136">加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：</span><span class="sxs-lookup"><span data-stu-id="1a24a-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="1a24a-137">下载 .NET 运行时、应用和应用依赖项。</span><span class="sxs-lookup"><span data-stu-id="1a24a-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="1a24a-138">初始化运行时以运行应用。</span><span class="sxs-lookup"><span data-stu-id="1a24a-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="1a24a-139">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="1a24a-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="1a24a-140"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="1a24a-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="1a24a-141">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="1a24a-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="1a24a-142">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="1a24a-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1a24a-143">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="1a24a-143">The template includes the following:</span></span>
  * <span data-ttu-id="1a24a-144">`_Host.cshtml` (Blazor Server)：实现为 Razor 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="1a24a-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="1a24a-145">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="1a24a-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="1a24a-146">加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="1a24a-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="1a24a-147">此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="1a24a-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="1a24a-148">`Counter` (`Pages/Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="1a24a-148">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="1a24a-149">`Error`（`Error.razor`，仅 Blazor Server 应用）：当应用中发生未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="1a24a-149">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="1a24a-150">`FetchData` (`Pages/FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="1a24a-150">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="1a24a-151">`Index` (`Pages/Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="1a24a-151">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="1a24a-152">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="1a24a-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="1a24a-153">`Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：</span><span class="sxs-lookup"><span data-stu-id="1a24a-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="1a24a-154">`MainLayout` (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="1a24a-154">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="1a24a-155">`NavMenu` (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="1a24a-155">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="1a24a-156">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="1a24a-156">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="1a24a-157"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="1a24a-157">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="1a24a-158">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="1a24a-158">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="1a24a-159">`Data` 文件夹 (Blazor Server)：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="1a24a-159">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="1a24a-160">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="1a24a-160">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="1a24a-161">`appsettings.json`：保留应用的[配置设置](xref:blazor/fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="1a24a-161">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="1a24a-162">在 Blazor WebAssembly 应用中，应用设置文件位于 `wwwroot` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1a24a-162">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="1a24a-163">在 Blazor Server 应用中，应用设置文件位于项目根目录中。</span><span class="sxs-lookup"><span data-stu-id="1a24a-163">In a Blazor Server app, the app settings file is located at the project root.</span></span>
