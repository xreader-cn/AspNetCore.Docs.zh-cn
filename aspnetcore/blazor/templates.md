---
title: ASP.NET Core Blazor 模板
author: guardrex
description: 了解 ASP.NET Core Blazor 应用程序模板和 Blazor 项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885507"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="24a5f-103">ASP.NET Core Blazor 模板</span><span class="sxs-lookup"><span data-stu-id="24a5f-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="24a5f-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="24a5f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="24a5f-105">Blazor 框架提供模板，用于为每个 Blazor 承载模型开发应用程序：</span><span class="sxs-lookup"><span data-stu-id="24a5f-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="24a5f-106"> WebAssembly （`blazorwasm`）</span><span class="sxs-lookup"><span data-stu-id="24a5f-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="24a5f-107"> 服务器（`blazorserver`）</span><span class="sxs-lookup"><span data-stu-id="24a5f-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="24a5f-108">有关 Blazor的宿主模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="24a5f-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="24a5f-109">有关基于模板创建 Blazor 应用程序的分步说明，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="24a5f-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="24a5f-110"> 项目结构</span><span class="sxs-lookup"><span data-stu-id="24a5f-110"> project structure</span></span>

<span data-ttu-id="24a5f-111">以下文件和文件夹构成了 Blazor 模板生成的 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="24a5f-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="24a5f-112">*Program.cs* &ndash; 应用程序的入口点，用于设置：</span><span class="sxs-lookup"><span data-stu-id="24a5f-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="24a5f-113">ASP.NET Core[主机](xref:fundamentals/host/generic-host)（Blazor Server）</span><span class="sxs-lookup"><span data-stu-id="24a5f-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="24a5f-114">WebAssembly host （Blazor WebAssembly） &ndash; 此文件中的代码对于从 Blazor WebAssembly 模板（`blazorwasm`）创建的应用程序是唯一的。</span><span class="sxs-lookup"><span data-stu-id="24a5f-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="24a5f-115">作为应用程序的根组件的 `App` 组件被指定为 `Add` 方法的 `app` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="24a5f-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="24a5f-116">服务可以在主机生成器上配置 `ConfigureServices` 方法（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>();`）。</span><span class="sxs-lookup"><span data-stu-id="24a5f-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="24a5f-117">可以通过主机生成器（`builder.Configuration`）提供配置。</span><span class="sxs-lookup"><span data-stu-id="24a5f-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="24a5f-118">*Startup.cs* （Blazor Server） &ndash; 包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="24a5f-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="24a5f-119">`Startup` 类定义两种方法：</span><span class="sxs-lookup"><span data-stu-id="24a5f-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="24a5f-120">`ConfigureServices` &ndash; 配置应用的[依赖项注入（DI）](xref:fundamentals/dependency-injection)服务。</span><span class="sxs-lookup"><span data-stu-id="24a5f-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="24a5f-121">在 Blazor Server apps 中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>添加服务，`WeatherForecastService` 将添加到服务容器中，供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="24a5f-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="24a5f-122">`Configure` &ndash; 配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="24a5f-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="24a5f-123">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> 来设置与浏览器进行实时连接时使用的终结点。</span><span class="sxs-lookup"><span data-stu-id="24a5f-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="24a5f-124">该连接是使用[SignalR](xref:signalr/introduction)创建的，它是一个框架，用于向应用程序添加实时 web 功能。</span><span class="sxs-lookup"><span data-stu-id="24a5f-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="24a5f-125">调用[MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)以设置应用的根页面（*Pages/_Host*），并启用导航。</span><span class="sxs-lookup"><span data-stu-id="24a5f-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="24a5f-126">*wwwroot/index.html* （Blazor WebAssembly） &ndash; 作为 html 页面实现的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="24a5f-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="24a5f-127">当最初请求应用的任何页面时，此页将呈现并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="24a5f-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="24a5f-128">页面指定 `App` 呈现根组件的位置。</span><span class="sxs-lookup"><span data-stu-id="24a5f-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="24a5f-129">`App` 组件（*app.config*）指定为 `Startup.Configure`中 `AddComponent` 方法的 `app` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="24a5f-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="24a5f-130">`_framework/blazor.webassembly.js` 加载 JavaScript 文件，该文件：</span><span class="sxs-lookup"><span data-stu-id="24a5f-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="24a5f-131">下载 .NET 运行时、应用程序和应用程序的依赖项。</span><span class="sxs-lookup"><span data-stu-id="24a5f-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="24a5f-132">初始化运行时以运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="24a5f-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="24a5f-133">*Pages/_Host cshtml* （Blazor Server） &ndash; 作为 Razor 页面实现的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="24a5f-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="24a5f-134">当最初请求应用的任何页面时，此页将呈现并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="24a5f-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="24a5f-135">`_framework/blazor.server.js` 加载 JavaScript 文件，该文件将设置浏览器与服务器之间的实时 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="24a5f-135">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="24a5f-136">"主机" 页指定根 `App` 组件（*app.config*）的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="24a5f-136">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="24a5f-137">*App.config* &ndash; 使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件设置客户端路由的应用程序的根组件。</span><span class="sxs-lookup"><span data-stu-id="24a5f-137">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="24a5f-138">`Router` 组件会截获浏览器导航，并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="24a5f-138">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="24a5f-139">*Pages*文件夹 &ndash; 包含组成 Blazor 应用的可路由组件/页面（*razor*）。</span><span class="sxs-lookup"><span data-stu-id="24a5f-139">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="24a5f-140">每页的路由都是使用[`@page`](xref:mvc/views/razor#page)指令指定的。</span><span class="sxs-lookup"><span data-stu-id="24a5f-140">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="24a5f-141">此模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="24a5f-141">The template includes the following components:</span></span>
  * <span data-ttu-id="24a5f-142">`Index` （&ndash;*razor*）实现主页。</span><span class="sxs-lookup"><span data-stu-id="24a5f-142">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="24a5f-143">`Counter` （*razor*） &ndash; 实现计数器页。</span><span class="sxs-lookup"><span data-stu-id="24a5f-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="24a5f-144">应用中发生未经处理的异常时，将 `Error` （仅限*Error*、Blazor Server app） &ndash; 呈现。</span><span class="sxs-lookup"><span data-stu-id="24a5f-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="24a5f-145">`FetchData` （*FetchData*） &ndash; 实现 "提取数据" 页。</span><span class="sxs-lookup"><span data-stu-id="24a5f-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="24a5f-146">*共享*文件夹 &ndash; 包含应用使用的其他 UI 组件（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="24a5f-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="24a5f-147">`MainLayout` （*MainLayout*） &ndash; 应用的布局组件。</span><span class="sxs-lookup"><span data-stu-id="24a5f-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="24a5f-148">`NavMenu` （*NavMenu*） &ndash; 实现侧栏导航。</span><span class="sxs-lookup"><span data-stu-id="24a5f-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="24a5f-149">包含[NavLink 组件](xref:blazor/routing#navlink-component)（<xref:Microsoft.AspNetCore.Components.Routing.NavLink>），该组件将呈现指向其他 Razor 组件的导航链接。</span><span class="sxs-lookup"><span data-stu-id="24a5f-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="24a5f-150">加载组件时，`NavLink` 组件会自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="24a5f-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="24a5f-151">*_Imports* &ndash; 包含包含在应用组件（*razor*）中的常见 razor 指令，如用于命名空间的[`@using`](xref:mvc/views/razor#using)指令。</span><span class="sxs-lookup"><span data-stu-id="24a5f-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="24a5f-152">*数据*文件夹（Blazor Server） &ndash; 包含向应用程序的 `FetchData` 组件提供示例天气数据的 `WeatherForecastService` 的 `WeatherForecast` 类和实现。</span><span class="sxs-lookup"><span data-stu-id="24a5f-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="24a5f-153">*wwwroot* &ndash; 包含应用的公共静态资产的应用的[Web 根](xref:fundamentals/index#web-root)文件夹。</span><span class="sxs-lookup"><span data-stu-id="24a5f-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="24a5f-154">*appsettings* （Blazor Server） &ndash; 应用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="24a5f-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
