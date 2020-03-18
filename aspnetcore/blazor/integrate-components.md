---
title: 将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用
author: guardrex
description: 了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649080"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="af263-103">将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="af263-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="af263-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="af263-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="af263-105">Razor 组件可以集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="af263-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="af263-106">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="af263-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="af263-107">准备应用以在页面和视图中使用组件</span><span class="sxs-lookup"><span data-stu-id="af263-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="af263-108">现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="af263-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="af263-109">在应用的布局文件 (_Layout.cshtml) 中：</span><span class="sxs-lookup"><span data-stu-id="af263-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="af263-110">将以下 `<base>` 标记添加到 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="af263-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="af263-111">前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。</span><span class="sxs-lookup"><span data-stu-id="af263-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="af263-112">如果应用是子应用程序，请按照 <xref:host-and-deploy/blazor/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="af263-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="af263-113">_Layout.cshtml 文件位于 Razor Pages 应用的 Pages/Shared 文件夹中，或 MVC 应用的 Views/Shared 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="af263-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="af263-114">紧接在 `</body>` 结束标记前面，添加 blazor.server.js 脚本的 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="af263-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="af263-115">框架会将 blazor.server.js 脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="af263-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="af263-116">无需手动将脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="af263-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="af263-117">将 _Imports.razor 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：</span><span class="sxs-lookup"><span data-stu-id="af263-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="af263-118">在 `Startup.ConfigureServices` 中，注册 Blazor 服务器服务：</span><span class="sxs-lookup"><span data-stu-id="af263-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="af263-119">在 `Startup.Configure` 中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：</span><span class="sxs-lookup"><span data-stu-id="af263-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="af263-120">将组件集成到任何页面或视图。</span><span class="sxs-lookup"><span data-stu-id="af263-120">Integrate components into any page or view.</span></span> <span data-ttu-id="af263-121">有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。</span><span class="sxs-lookup"><span data-stu-id="af263-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="af263-122">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="af263-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="af263-123">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="af263-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="af263-124">在 Razor Pages 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="af263-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="af263-125">按照[准备应用以在页面和视图中使用组件](#prepare-the-app-to-use-components-in-pages-and-views)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="af263-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="af263-126">将具有以下内容的 App.razor 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="af263-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="af263-127">将具有以下内容的 _Host.cshtml 文件添加到 Pages 文件夹：</span><span class="sxs-lookup"><span data-stu-id="af263-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="af263-128">组件将共享 _Layout.cshtml 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="af263-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="af263-129">在 `Startup.Configure` 中，将 _Host.cshtml 的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="af263-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="af263-130">将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="af263-130">Add routable components to the app.</span></span> <span data-ttu-id="af263-131">例如：</span><span class="sxs-lookup"><span data-stu-id="af263-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="af263-132">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="af263-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="af263-133">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="af263-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="af263-134">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="af263-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="af263-135">在 MVC 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="af263-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="af263-136">按照[准备应用以在页面和视图中使用组件](#prepare-the-app-to-use-components-in-pages-and-views)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="af263-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="af263-137">将具有以下内容的 App.razor 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="af263-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="af263-138">将具有以下内容的 _Host.cshtml 文件添加到 Views/Home 文件夹：</span><span class="sxs-lookup"><span data-stu-id="af263-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="af263-139">组件将共享 _Layout.cshtml 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="af263-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="af263-140">向主控制器添加操作：</span><span class="sxs-lookup"><span data-stu-id="af263-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="af263-141">在 `Startup.Configure` 中，将返回 _Host.cshtml 视图的控制器操作的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="af263-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="af263-142">创建 Pages 文件夹并将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="af263-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="af263-143">例如：</span><span class="sxs-lookup"><span data-stu-id="af263-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="af263-144">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="af263-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="af263-145">组件命名空间</span><span class="sxs-lookup"><span data-stu-id="af263-145">Component namespaces</span></span>

<span data-ttu-id="af263-146">使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 _ViewImports.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="af263-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="af263-147">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="af263-147">In the following example:</span></span>

* <span data-ttu-id="af263-148">将 `MyAppNamespace` 更改为应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="af263-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="af263-149">如果不使用名为 Components 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="af263-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="af263-150">_ViewImports.cshtml 文件位于 Razor Pages 应用的 Pages 文件夹中，或是 MVC 应用的 Views 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="af263-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="af263-151">有关详细信息，请参阅 <xref:blazor/components#import-components>。</span><span class="sxs-lookup"><span data-stu-id="af263-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="af263-152">从页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="af263-152">Render components from a page or view</span></span>

<span data-ttu-id="af263-153">本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。</span><span class="sxs-lookup"><span data-stu-id="af263-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="af263-154">若要从页面或视图呈现组件，请使用 `Component` 标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="af263-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="af263-155">参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置的属性。</span><span class="sxs-lookup"><span data-stu-id="af263-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="af263-156">例如，可以指定 `IncrementAmount` 的值，因为 `IncrementAmount` 的类型为 `int`，这是 JSON 序列化程序所支持的基元类型。</span><span class="sxs-lookup"><span data-stu-id="af263-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="af263-157">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="af263-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="af263-158">已在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="af263-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="af263-159">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="af263-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="af263-160">描述</span><span class="sxs-lookup"><span data-stu-id="af263-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="af263-161">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="af263-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="af263-162">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="af263-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="af263-163">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="af263-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="af263-164">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="af263-164">Output from the component isn't included.</span></span> <span data-ttu-id="af263-165">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="af263-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="af263-166">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="af263-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="af263-167">尽管页面和视图可以使用组件，但反过来则不行。</span><span class="sxs-lookup"><span data-stu-id="af263-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="af263-168">组件无法使用特定于视图和页面的方案，如分部视图和部分。</span><span class="sxs-lookup"><span data-stu-id="af263-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="af263-169">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解到组件中。</span><span class="sxs-lookup"><span data-stu-id="af263-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="af263-170">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="af263-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="af263-171">有关如何呈现组件、组件状态以及 `Component` 标记帮助程序的详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="af263-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
