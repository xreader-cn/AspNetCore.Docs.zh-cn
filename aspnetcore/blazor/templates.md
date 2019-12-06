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
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="1f154-103">ASP.NET Core Blazor 模板</span><span class="sxs-lookup"><span data-stu-id="1f154-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="1f154-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1f154-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1f154-105">Blazor 框架提供模板，用于为每个 Blazor 承载模型开发应用程序：</span><span class="sxs-lookup"><span data-stu-id="1f154-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="1f154-106"> WebAssembly （`blazorwasm`）</span><span class="sxs-lookup"><span data-stu-id="1f154-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="1f154-107"> 服务器（`blazorserver`）</span><span class="sxs-lookup"><span data-stu-id="1f154-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="1f154-108">有关 Blazor的宿主模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="1f154-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="1f154-109">有关基于模板创建 Blazor 应用程序的分步说明，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="1f154-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="1f154-110"> 项目结构</span><span class="sxs-lookup"><span data-stu-id="1f154-110"> project structure</span></span>

<span data-ttu-id="1f154-111">以下文件和文件夹构成了 Blazor 模板生成的 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="1f154-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="1f154-112">*Program.cs* &ndash; 设置 ASP.NET Core[主机](xref:fundamentals/host/generic-host)的应用入口点。</span><span class="sxs-lookup"><span data-stu-id="1f154-112">*Program.cs* &ndash; The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="1f154-113">此文件中的代码对于从 ASP.NET Core 模板生成的所有 ASP.NET Core 应用都是通用的。</span><span class="sxs-lookup"><span data-stu-id="1f154-113">The code in this file is common to all ASP.NET Core apps generated from ASP.NET Core templates.</span></span>

* <span data-ttu-id="1f154-114">*Startup.cs* &ndash; 包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="1f154-114">*Startup.cs* &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="1f154-115">`Startup` 类定义两种方法：</span><span class="sxs-lookup"><span data-stu-id="1f154-115">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="1f154-116">`ConfigureServices` &ndash; 配置应用的[依赖项注入（DI）](xref:fundamentals/dependency-injection)服务。</span><span class="sxs-lookup"><span data-stu-id="1f154-116">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="1f154-117">在 Blazor Server apps 中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>添加服务，`WeatherForecastService` 将添加到服务容器中，供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="1f154-117">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="1f154-118">`Configure` &ndash; 配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="1f154-118">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * Blazor<span data-ttu-id="1f154-119"> WebAssembly &ndash; 将 `App` 组件（指定为 `app` DOM 元素添加到 `AddComponent` 方法），这是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="1f154-119"> WebAssembly &ndash; Adds the `App` component (specified as the `app` DOM element to the `AddComponent` method), which is the root component of the app.</span></span>
    * Blazor<span data-ttu-id="1f154-120"> 服务器</span><span class="sxs-lookup"><span data-stu-id="1f154-120"> Server</span></span>
      * <span data-ttu-id="1f154-121">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> 来设置与浏览器进行实时连接时使用的终结点。</span><span class="sxs-lookup"><span data-stu-id="1f154-121"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="1f154-122">该连接是使用[SignalR](xref:signalr/introduction)创建的，它是一个框架，用于向应用程序添加实时 web 功能。</span><span class="sxs-lookup"><span data-stu-id="1f154-122">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
      * <span data-ttu-id="1f154-123">调用[MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)以设置应用的根页面（*Pages/_Host*），并启用导航。</span><span class="sxs-lookup"><span data-stu-id="1f154-123">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="1f154-124">*wwwroot/index.html* （Blazor WebAssembly） &ndash; 作为 html 页面实现的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="1f154-124">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="1f154-125">当最初请求应用的任何页面时，此页将呈现并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="1f154-125">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="1f154-126">页面指定 `App` 呈现根组件的位置。</span><span class="sxs-lookup"><span data-stu-id="1f154-126">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="1f154-127">`App` 组件（*app.config*）指定为 `Startup.Configure`中 `AddComponent` 方法的 `app` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="1f154-127">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="1f154-128">已加载 *_framework/Blazor.webassembly.js* JavaScript 文件，该文件：</span><span class="sxs-lookup"><span data-stu-id="1f154-128">The *_framework/blazor.webassembly.js* JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="1f154-129">下载 .NET 运行时、应用程序和应用程序的依赖项。</span><span class="sxs-lookup"><span data-stu-id="1f154-129">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="1f154-130">初始化运行时以运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="1f154-130">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="1f154-131">*Pages/_Host cshtml* （Blazor Server） &ndash; 作为 Razor 页面实现的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="1f154-131">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="1f154-132">当最初请求应用的任何页面时，此页将呈现并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="1f154-132">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="1f154-133">加载 *_framework/Blazor.server.js* JavaScript 文件，该文件将设置浏览器与服务器之间的实时 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="1f154-133">The *_framework/blazor.server.js* JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="1f154-134">"主机" 页指定根 `App` 组件（*app.config*）的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="1f154-134">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="1f154-135">*App.config* &ndash; 使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件设置客户端路由的应用程序的根组件。</span><span class="sxs-lookup"><span data-stu-id="1f154-135">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="1f154-136">`Router` 组件会截获浏览器导航，并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="1f154-136">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="1f154-137">*Pages*文件夹 &ndash; 包含组成 Blazor 应用的可路由组件/页面（*razor*）。</span><span class="sxs-lookup"><span data-stu-id="1f154-137">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="1f154-138">每页的路由都是使用[`@page`](xref:mvc/views/razor#page)指令指定的。</span><span class="sxs-lookup"><span data-stu-id="1f154-138">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1f154-139">此模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="1f154-139">The template includes the following components:</span></span>
  * <span data-ttu-id="1f154-140">`Index` （&ndash;*razor*）实现主页。</span><span class="sxs-lookup"><span data-stu-id="1f154-140">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="1f154-141">`Counter` （*razor*） &ndash; 实现计数器页。</span><span class="sxs-lookup"><span data-stu-id="1f154-141">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="1f154-142">应用中发生未经处理的异常时，将 `Error` （仅限*Error*、Blazor Server app） &ndash; 呈现。</span><span class="sxs-lookup"><span data-stu-id="1f154-142">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="1f154-143">`FetchData` （*FetchData*） &ndash; 实现 "提取数据" 页。</span><span class="sxs-lookup"><span data-stu-id="1f154-143">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="1f154-144">*共享*文件夹 &ndash; 包含应用使用的其他 UI 组件（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="1f154-144">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="1f154-145">`MainLayout` （*MainLayout*） &ndash; 应用的布局组件。</span><span class="sxs-lookup"><span data-stu-id="1f154-145">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="1f154-146">`NavMenu` （*NavMenu*） &ndash; 实现侧栏导航。</span><span class="sxs-lookup"><span data-stu-id="1f154-146">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="1f154-147">包含[NavLink 组件](xref:blazor/routing#navlink-component)（<xref:Microsoft.AspNetCore.Components.Routing.NavLink>），该组件将呈现指向其他 Razor 组件的导航链接。</span><span class="sxs-lookup"><span data-stu-id="1f154-147">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="1f154-148">加载组件时，`NavLink` 组件会自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="1f154-148">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="1f154-149">*_Imports* &ndash; 包含包含在应用组件（*razor*）中的常见 razor 指令，如用于命名空间的[`@using`](xref:mvc/views/razor#using)指令。</span><span class="sxs-lookup"><span data-stu-id="1f154-149">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="1f154-150">*数据*文件夹（Blazor Server） &ndash; 包含向应用程序的 `FetchData` 组件提供示例天气数据的 `WeatherForecastService` 的 `WeatherForecast` 类和实现。</span><span class="sxs-lookup"><span data-stu-id="1f154-150">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="1f154-151">*wwwroot* &ndash; 包含应用的公共静态资产的应用的[Web 根](xref:fundamentals/index#web-root)文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f154-151">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="1f154-152">*appsettings* （Blazor Server） &ndash; 应用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="1f154-152">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
