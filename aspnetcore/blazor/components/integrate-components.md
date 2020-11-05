---
title: '将 ASP.NET Core :::no-loc(Razor)::: 组件集成到 :::no-loc(Razor)::: Pages 和 MVC 应用'
author: guardrex
description: '了解适用于 :::no-loc(Blazor)::: 应用中的组件和 DOM 元素的数据绑定方案。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056252"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="bc89a-103">将 ASP.NET Core :::no-loc(Razor)::: 组件集成到 :::no-loc(Razor)::: Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="bc89a-103">Integrate ASP.NET Core :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps</span></span>

<span data-ttu-id="bc89a-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bc89a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="bc89a-105">:::no-loc(Razor)::: 组件可以集成到 :::no-loc(Razor)::: Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="bc89a-106">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="bc89a-107">[准备好应用](#prepare-the-app)后，根据应用的要求按照下列部分中的指南操作：</span><span class="sxs-lookup"><span data-stu-id="bc89a-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="bc89a-108">可路由组件：针对可直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="bc89a-109">如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="bc89a-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="bc89a-110">在 :::no-loc(Razor)::: Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-110">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="bc89a-111">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="bc89a-112">[从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="bc89a-113">如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="bc89a-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="bc89a-114">准备应用</span><span class="sxs-lookup"><span data-stu-id="bc89a-114">Prepare the app</span></span>

<span data-ttu-id="bc89a-115">现有 :::no-loc(Razor)::: Pages 或 MVC 应用可以将 :::no-loc(Razor)::: 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="bc89a-115">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="bc89a-116">在应用的布局文件 (`_Layout.cshtml`) 中：</span><span class="sxs-lookup"><span data-stu-id="bc89a-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="bc89a-117">将以下 `<base>` 标记添加到 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="bc89a-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="bc89a-118">前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。</span><span class="sxs-lookup"><span data-stu-id="bc89a-118">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="bc89a-119">如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="bc89a-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="bc89a-120">`_Layout.cshtml` 文件位于 :::no-loc(Razor)::: Pages 应用的 Pages/Shared 文件夹中，或 MVC 应用的 Views/Shared 文件夹中 。</span><span class="sxs-lookup"><span data-stu-id="bc89a-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a :::no-loc(Razor)::: Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="bc89a-121">紧接在 `</body>` 结束标记前面，添加 blazor.server.js 脚本的 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="bc89a-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="bc89a-122">框架会将 blazor.server.js 脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="bc89a-123">无需手动将脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="bc89a-124">将 `_Imports.razor` 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：</span><span class="sxs-lookup"><span data-stu-id="bc89a-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="bc89a-125">在 `Startup.ConfigureServices`中，注册 :::no-loc(Blazor Server)::: 服务：</span><span class="sxs-lookup"><span data-stu-id="bc89a-125">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="bc89a-126">在 `Startup.Configure` 中，将 :::no-loc(Blazor)::: 中心终结点添加到 `app.UseEndpoints`：</span><span class="sxs-lookup"><span data-stu-id="bc89a-126">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="bc89a-127">将组件集成到任何页面或视图。</span><span class="sxs-lookup"><span data-stu-id="bc89a-127">Integrate components into any page or view.</span></span> <span data-ttu-id="bc89a-128">有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。</span><span class="sxs-lookup"><span data-stu-id="bc89a-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="bc89a-129">在 :::no-loc(Razor)::: Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-129">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="bc89a-130">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="bc89a-131">在 :::no-loc(Razor)::: Pages 应用中支持可路由 :::no-loc(Razor)::: 组件：</span><span class="sxs-lookup"><span data-stu-id="bc89a-131">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="bc89a-132">按照[准备应用](#prepare-the-app)部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="bc89a-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="bc89a-133">将具有以下内容的 `App.razor` 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="bc89a-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="bc89a-134">将具有以下内容的 `_Host.cshtml` 文件添加到 `Pages` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="bc89a-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="bc89a-135">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="bc89a-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="bc89a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="bc89a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="bc89a-137">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="bc89a-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="bc89a-138">在页面上呈现为静态 HTML，或者包含从用户代理启动 :::no-loc(Blazor)::: 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="bc89a-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="bc89a-139">呈现模式</span><span class="sxs-lookup"><span data-stu-id="bc89a-139">Render Mode</span></span> | <span data-ttu-id="bc89a-140">描述</span><span class="sxs-lookup"><span data-stu-id="bc89a-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bc89a-141">在静态 HTML 中呈现 `App` 组件，并包含 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="bc89a-141">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bc89a-142">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-142">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bc89a-143">呈现 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="bc89a-143">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bc89a-144">不包括 `App` 组件的输出。</span><span class="sxs-lookup"><span data-stu-id="bc89a-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="bc89a-145">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-145">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bc89a-146">在静态 HTML 中呈现 `App` 组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="bc89a-147">要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="bc89a-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="bc89a-148">在 `Startup.Configure` 中，将 `_Host.cshtml` 页的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="bc89a-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="bc89a-149">将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-149">Add routable components to the app.</span></span> <span data-ttu-id="bc89a-150">例如：</span><span class="sxs-lookup"><span data-stu-id="bc89a-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="bc89a-151">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="bc89a-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="bc89a-152">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="bc89a-153">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="bc89a-154">在 MVC 应用中支持可路由 :::no-loc(Razor)::: 组件：</span><span class="sxs-lookup"><span data-stu-id="bc89a-154">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="bc89a-155">按照[准备应用](#prepare-the-app)部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="bc89a-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="bc89a-156">将具有以下内容的 `App.razor` 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="bc89a-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="bc89a-157">将具有以下内容的 `_Host.cshtml` 文件添加到 `Views/Home` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="bc89a-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="bc89a-158">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="bc89a-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="bc89a-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="bc89a-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="bc89a-160">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="bc89a-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="bc89a-161">在页面上呈现为静态 HTML，或者包含从用户代理启动 :::no-loc(Blazor)::: 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="bc89a-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="bc89a-162">呈现模式</span><span class="sxs-lookup"><span data-stu-id="bc89a-162">Render Mode</span></span> | <span data-ttu-id="bc89a-163">描述</span><span class="sxs-lookup"><span data-stu-id="bc89a-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bc89a-164">在静态 HTML 中呈现 `App` 组件，并包含 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="bc89a-164">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bc89a-165">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-165">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bc89a-166">呈现 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="bc89a-166">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bc89a-167">不包括 `App` 组件的输出。</span><span class="sxs-lookup"><span data-stu-id="bc89a-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="bc89a-168">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-168">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bc89a-169">在静态 HTML 中呈现 `App` 组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="bc89a-170">要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="bc89a-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="bc89a-171">向主控制器添加操作：</span><span class="sxs-lookup"><span data-stu-id="bc89a-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="bc89a-172">在 `Startup.Configure` 中，将返回 `_Host.cshtml` 视图的控制器操作的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="bc89a-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="bc89a-173">创建 `Pages` 文件夹并将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="bc89a-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="bc89a-174">例如：</span><span class="sxs-lookup"><span data-stu-id="bc89a-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="bc89a-175">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="bc89a-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="bc89a-176">从页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-176">Render components from a page or view</span></span>

<span data-ttu-id="bc89a-177">本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。</span><span class="sxs-lookup"><span data-stu-id="bc89a-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="bc89a-178">若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="bc89a-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="bc89a-179">呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-179">Render stateful interactive components</span></span>

<span data-ttu-id="bc89a-180">可以将有状态的交互式组件添加到 :::no-loc(Razor)::: 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="bc89a-180">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="bc89a-181">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="bc89a-181">When the page or view renders:</span></span>

* <span data-ttu-id="bc89a-182">该组件通过页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="bc89a-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="bc89a-183">用于预呈现的初始组件状态丢失。</span><span class="sxs-lookup"><span data-stu-id="bc89a-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="bc89a-184">建立 :::no-loc(SignalR)::: 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="bc89a-184">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="bc89a-185">以下 :::no-loc(Razor)::: 页面将呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="bc89a-185">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="bc89a-186">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="bc89a-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="bc89a-187">呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="bc89a-187">Render noninteractive components</span></span>

<span data-ttu-id="bc89a-188">在以下 :::no-loc(Razor)::: 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-188">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="bc89a-189">由于该组件是以静态方式呈现的，因此它不是交互式组件：</span><span class="sxs-lookup"><span data-stu-id="bc89a-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="bc89a-190">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="bc89a-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="bc89a-191">组件命名空间</span><span class="sxs-lookup"><span data-stu-id="bc89a-191">Component namespaces</span></span>

<span data-ttu-id="bc89a-192">使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 `_ViewImports.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="bc89a-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="bc89a-193">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="bc89a-193">In the following example:</span></span>

* <span data-ttu-id="bc89a-194">将 `MyAppNamespace` 更改为应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="bc89a-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="bc89a-195">如果不使用名为 Components 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="bc89a-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="bc89a-196">`_ViewImports.cshtml` 文件位于 :::no-loc(Razor)::: Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="bc89a-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="bc89a-197">有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="bc89a-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
