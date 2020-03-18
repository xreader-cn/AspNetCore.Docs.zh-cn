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
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="fc79e-103">ASP.NET Core Blazor 模板</span><span class="sxs-lookup"><span data-stu-id="fc79e-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="fc79e-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc79e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fc79e-105">Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型开发应用：</span><span class="sxs-lookup"><span data-stu-id="fc79e-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="fc79e-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="fc79e-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="fc79e-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="fc79e-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="fc79e-108">有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="fc79e-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="fc79e-109">有关基于模板创建 Blazor 应用的分步说明，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="fc79e-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="fc79e-110"> 项目结构</span><span class="sxs-lookup"><span data-stu-id="fc79e-110"> project structure</span></span>

<span data-ttu-id="fc79e-111">以下文件和文件夹构成了基于 Blazor 模板生成的 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="fc79e-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="fc79e-112">*Program.cs*&ndash; 应用的入口点，用于设置以下各项：</span><span class="sxs-lookup"><span data-stu-id="fc79e-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="fc79e-113">ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="fc79e-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="fc79e-114">WebAssembly 主机 (Blazor WebAssembly) &ndash; 该文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。</span><span class="sxs-lookup"><span data-stu-id="fc79e-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="fc79e-115">作为应用根组件的 `App` 组件被指定为 `Add` 方法的 `app` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="fc79e-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="fc79e-116">可以在主机生成器上使用 `ConfigureServices` 方法配置服务（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>();`）。</span><span class="sxs-lookup"><span data-stu-id="fc79e-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="fc79e-117">可以通过主机生成器提供配置 (`builder.Configuration`)。</span><span class="sxs-lookup"><span data-stu-id="fc79e-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="fc79e-118">*Startup.cs* (Blazor Server) &ndash; 包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="fc79e-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="fc79e-119">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="fc79e-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="fc79e-120">`ConfigureServices`&ndash; 配置应用的 [ 依赖项注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="fc79e-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="fc79e-121">在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="fc79e-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="fc79e-122">`Configure`&ndash; 配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="fc79e-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="fc79e-123">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="fc79e-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="fc79e-124">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="fc79e-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="fc79e-125">调用 [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 可以设置应用的根页面 (*Pages/_Host.cshtml*) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="fc79e-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="fc79e-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; 实现为 HTML 页面的应用根页面：</span><span class="sxs-lookup"><span data-stu-id="fc79e-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="fc79e-127">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="fc79e-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="fc79e-128">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="fc79e-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="fc79e-129">`App` 组件 (*App.razor*) 在 `Startup.Configure` 中指定为 `AddComponent` 方法的 `app` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="fc79e-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="fc79e-130">加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：</span><span class="sxs-lookup"><span data-stu-id="fc79e-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="fc79e-131">下载 .NET 运行时、应用和应用依赖项。</span><span class="sxs-lookup"><span data-stu-id="fc79e-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="fc79e-132">初始化运行时以运行应用。</span><span class="sxs-lookup"><span data-stu-id="fc79e-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="fc79e-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; 实现为 Razor 页面的应用根页面：</span><span class="sxs-lookup"><span data-stu-id="fc79e-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="fc79e-134">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="fc79e-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="fc79e-135">加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="fc79e-135">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="fc79e-136">“主机”页面指定根 `App` 组件 (*App.razor*) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="fc79e-136">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="fc79e-137">*App.razor*&ndash; 应用的根组件，它使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="fc79e-137">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="fc79e-138">`Router` 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="fc79e-138">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="fc79e-139">*Pages* 文件夹 &ndash; 包含构成 Blazor 应用的可路由组件/页面 ( *.razor*)。</span><span class="sxs-lookup"><span data-stu-id="fc79e-139">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="fc79e-140">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="fc79e-140">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="fc79e-141">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="fc79e-141">The template includes the following components:</span></span>
  * <span data-ttu-id="fc79e-142">`Index` (*Index.razor*) &ndash; 实现主页。</span><span class="sxs-lookup"><span data-stu-id="fc79e-142">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="fc79e-143">`Counter` (*Counter.razor*) &ndash; 实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="fc79e-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="fc79e-144">`Error`（*Error.razor*，仅限 Blazor Server 应用）&ndash; 当应用中出现未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="fc79e-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="fc79e-145">`FetchData` (*FetchData.razor*) &ndash; 实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="fc79e-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="fc79e-146">*Shared* 文件夹 &ndash; 包含应用使用的其他 UI 组件 ( *.razor*)：</span><span class="sxs-lookup"><span data-stu-id="fc79e-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="fc79e-147">`MainLayout` (*MainLayout.razor*) &ndash; 应用的布局组件。</span><span class="sxs-lookup"><span data-stu-id="fc79e-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="fc79e-148">`NavMenu` (*NavMenu.razor*) &ndash; 实现侧栏导航。</span><span class="sxs-lookup"><span data-stu-id="fc79e-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="fc79e-149">包括 [NavLink 组件](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="fc79e-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="fc79e-150">`NavLink` 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="fc79e-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="fc79e-151">*_Imports.razor*&ndash; 包括要包含在应用组件 ( *.razor*) 中的常见 Razor 指令，例如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="fc79e-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="fc79e-152">*Data* 文件夹 (Blazor Server) &ndash; 包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="fc79e-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="fc79e-153">*wwwroot*&ndash; 应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="fc79e-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="fc79e-154">*appsettings.json* (Blazor Server) &ndash; 应用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="fc79e-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
