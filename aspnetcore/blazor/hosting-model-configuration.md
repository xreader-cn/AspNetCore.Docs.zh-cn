---
title: ASP.NET Core Blazor 宿主模型配置
author: guardrex
description: 了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214939"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="70bcf-103">ASP.NET Core Blazor 宿主模型配置</span><span class="sxs-lookup"><span data-stu-id="70bcf-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="70bcf-104">作者： [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="70bcf-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="70bcf-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="70bcf-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="70bcf-106">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="70bcf-106">Blazor Server</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="70bcf-107">将 Razor 组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="70bcf-107">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="70bcf-108">使用页面和视图中的组件</span><span class="sxs-lookup"><span data-stu-id="70bcf-108">Use components in pages and views</span></span>

<span data-ttu-id="70bcf-109">现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="70bcf-109">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="70bcf-110">在应用的布局文件（ *_Layout cshtml*）中：</span><span class="sxs-lookup"><span data-stu-id="70bcf-110">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="70bcf-111">将以下 `<base>` 标记添加到 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="70bcf-111">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="70bcf-112">上一示例中的 `href` 值（*应用程序基路径*）假定应用位于根 URL 路径（`/`）。</span><span class="sxs-lookup"><span data-stu-id="70bcf-112">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="70bcf-113">如果应用是子应用程序，请按照 <xref:host-and-deploy/blazor/index#app-base-path> 文章的*应用程序基路径*部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="70bcf-113">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="70bcf-114">*_Layout*的文件位于一个 Razor Pages 应用中的*Pages/shared*文件夹中，或位于 MVC 应用程序的*视图/共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-114">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="70bcf-115">在关闭 `</body>` 标记之前，添加*blazor*脚本的 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="70bcf-115">Add a `<script>` tag for the *blazor.server.js* script right before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="70bcf-116">框架将*blazor*脚本添加到应用程序。</span><span class="sxs-lookup"><span data-stu-id="70bcf-116">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="70bcf-117">无需手动将脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="70bcf-117">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="70bcf-118">将 *_Imports razor*文件添加到具有以下内容的项目的根文件夹中（将最后一个命名空间 `MyAppNamespace`更改为应用的命名空间）：</span><span class="sxs-lookup"><span data-stu-id="70bcf-118">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="70bcf-119">在 `Startup.ConfigureServices`中，注册 Blazor Server 服务：</span><span class="sxs-lookup"><span data-stu-id="70bcf-119">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="70bcf-120">在 `Startup.Configure`中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：</span><span class="sxs-lookup"><span data-stu-id="70bcf-120">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="70bcf-121">将组件集成到任何页面或视图中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-121">Integrate components into any page or view.</span></span> <span data-ttu-id="70bcf-122">有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> 文章的将*组件集成到 Razor Pages 和 MVC 应用程序*部分。</span><span class="sxs-lookup"><span data-stu-id="70bcf-122">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="70bcf-123">在 Razor Pages 应用程序中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="70bcf-123">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="70bcf-124">支持 Razor Pages 应用中的可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="70bcf-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="70bcf-125">按照[使用页面和视图中的组件](#use-components-in-pages-and-views)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="70bcf-125">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="70bcf-126">将一个*app.config*文件添加到项目的根目录，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="70bcf-126">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="70bcf-127">将 *_Host 的 cshtml*文件添加到*Pages*文件夹，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="70bcf-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="70bcf-128">组件使用共享的 *_Layout cshtml*文件进行布局。</span><span class="sxs-lookup"><span data-stu-id="70bcf-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="70bcf-129">将 *_Host cshtml*页的低优先级路由添加到 `Startup.Configure`中的终结点配置：</span><span class="sxs-lookup"><span data-stu-id="70bcf-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="70bcf-130">将可路由的组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="70bcf-130">Add routable components to the app.</span></span> <span data-ttu-id="70bcf-131">例如：</span><span class="sxs-lookup"><span data-stu-id="70bcf-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="70bcf-132">使用自定义文件夹保存应用程序的组件时，将表示文件夹的命名空间添加到*Pages/_ViewImports cshtml*文件中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-132">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="70bcf-133">有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>。</span><span class="sxs-lookup"><span data-stu-id="70bcf-133">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="70bcf-134">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="70bcf-134">Use routable components in an MVC app</span></span>

<span data-ttu-id="70bcf-135">在 MVC 应用中支持可路由的 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="70bcf-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="70bcf-136">按照[使用页面和视图中的组件](#use-components-in-pages-and-views)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="70bcf-136">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="70bcf-137">将一个*app.config*文件添加到项目的根目录，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="70bcf-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="70bcf-138">将 *_Host 的 cshtml*文件添加到具有以下内容的*Views/Home*文件夹中：</span><span class="sxs-lookup"><span data-stu-id="70bcf-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="70bcf-139">组件使用共享的 *_Layout cshtml*文件进行布局。</span><span class="sxs-lookup"><span data-stu-id="70bcf-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="70bcf-140">向 Home 控制器添加操作：</span><span class="sxs-lookup"><span data-stu-id="70bcf-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="70bcf-141">将返回 *_Host*的控制器操作的低优先级路由添加到 `Startup.Configure`中的终结点配置：</span><span class="sxs-lookup"><span data-stu-id="70bcf-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="70bcf-142">创建*Pages*文件夹，并将可路由的组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="70bcf-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="70bcf-143">例如：</span><span class="sxs-lookup"><span data-stu-id="70bcf-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="70bcf-144">使用自定义文件夹保存应用程序的组件时，将表示文件夹的命名空间添加到*Views/_ViewImports cshtml*文件中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-144">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="70bcf-145">有关详细信息，请参阅 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>。</span><span class="sxs-lookup"><span data-stu-id="70bcf-145">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="70bcf-146">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="70bcf-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="70bcf-147">当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="70bcf-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="70bcf-148">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="70bcf-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="70bcf-149">若要自定义 UI，请在 *_Host* Razor page 的 `<body>` 中定义具有 `components-reconnect-modal` `id` 的元素：</span><span class="sxs-lookup"><span data-stu-id="70bcf-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="70bcf-150">下表描述了应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="70bcf-150">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="70bcf-151">CSS 类</span><span class="sxs-lookup"><span data-stu-id="70bcf-151">CSS class</span></span>                       | <span data-ttu-id="70bcf-152">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="70bcf-152">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="70bcf-153">断开连接。</span><span class="sxs-lookup"><span data-stu-id="70bcf-153">A lost connection.</span></span> <span data-ttu-id="70bcf-154">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="70bcf-154">The client is attempting to reconnect.</span></span> <span data-ttu-id="70bcf-155">显示模式。</span><span class="sxs-lookup"><span data-stu-id="70bcf-155">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="70bcf-156">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="70bcf-156">An active connection is re-established to the server.</span></span> <span data-ttu-id="70bcf-157">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="70bcf-157">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="70bcf-158">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="70bcf-158">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="70bcf-159">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="70bcf-159">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="70bcf-160">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="70bcf-160">Reconnection rejected.</span></span> <span data-ttu-id="70bcf-161">服务器已达到但拒绝了连接，并且服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="70bcf-161">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="70bcf-162">若要重新加载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="70bcf-162">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="70bcf-163">当以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="70bcf-163">This connection state may result when:</span></span><ul><li><span data-ttu-id="70bcf-164">服务器端线路发生崩溃。</span><span class="sxs-lookup"><span data-stu-id="70bcf-164">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="70bcf-165">断开客户端的连接时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="70bcf-165">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="70bcf-166">将释放用户与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="70bcf-166">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="70bcf-167">服务器已重新启动，或者应用程序的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="70bcf-167">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="70bcf-168">呈现模式</span><span class="sxs-lookup"><span data-stu-id="70bcf-168">Render mode</span></span>

<span data-ttu-id="70bcf-169">默认情况下，Blazor 服务器应用程序设置为在客户端与服务器建立连接之前，在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="70bcf-169">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="70bcf-170">这是在 *_Host*的 "Razor" Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="70bcf-170">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="70bcf-171">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="70bcf-171">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="70bcf-172">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-172">Is prerendered into the page.</span></span>
* <span data-ttu-id="70bcf-173">在页面上呈现为静态 HTML，或者，如果包含从用户代理启动 Blazor 应用所需的信息，则为。</span><span class="sxs-lookup"><span data-stu-id="70bcf-173">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="70bcf-174">说明</span><span class="sxs-lookup"><span data-stu-id="70bcf-174">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="70bcf-175">将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。</span><span class="sxs-lookup"><span data-stu-id="70bcf-175">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="70bcf-176">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="70bcf-176">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="70bcf-177">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="70bcf-177">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="70bcf-178">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="70bcf-178">Output from the component isn't included.</span></span> <span data-ttu-id="70bcf-179">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="70bcf-179">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="70bcf-180">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="70bcf-180">Renders the component into static HTML.</span></span> |

<span data-ttu-id="70bcf-181">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="70bcf-181">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="70bcf-182">从 Razor 页面和视图呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="70bcf-182">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="70bcf-183">可以将有状态交互组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="70bcf-183">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="70bcf-184">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="70bcf-184">When the page or view renders:</span></span>

* <span data-ttu-id="70bcf-185">该组件与页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="70bcf-185">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="70bcf-186">用于预呈现的初始组件状态将丢失。</span><span class="sxs-lookup"><span data-stu-id="70bcf-186">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="70bcf-187">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="70bcf-187">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="70bcf-188">以下 Razor 页面将呈现一个 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="70bcf-188">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="70bcf-189">从 Razor 页面和视图呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="70bcf-189">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="70bcf-190">在以下 Razor 页面中，使用以下格式使用指定的初始值静态呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="70bcf-190">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="70bcf-191">由于 `MyComponent` 是以静态方式呈现的，因此该组件不能是交互式的。</span><span class="sxs-lookup"><span data-stu-id="70bcf-191">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="70bcf-192">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="70bcf-192">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="70bcf-193">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="70bcf-193">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="70bcf-194">例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="70bcf-194">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="70bcf-195">若要在*Pages/_Host* # 文件中配置 SignalR 客户端：</span><span class="sxs-lookup"><span data-stu-id="70bcf-195">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="70bcf-196">将 `autostart="false"` 特性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="70bcf-196">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="70bcf-197">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="70bcf-197">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
