---
<span data-ttu-id="5033a-101">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-103">'Blazor'</span></span>
- <span data-ttu-id="5033a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-104">'Identity'</span></span>
- <span data-ttu-id="5033a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-106">'Razor'</span></span>
- <span data-ttu-id="5033a-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5033a-108">将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="5033a-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5033a-109">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5033a-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="5033a-110"> 组件可以集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5033a-111">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5033a-112">[准备好应用](#prepare-the-app)后，根据应用的要求按照下列部分中的指南操作：</span><span class="sxs-lookup"><span data-stu-id="5033a-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="5033a-113">可路由组件：针对可直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="5033a-114">如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="5033a-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="5033a-115">[在 Razor Pages 应用中使用可路由组件](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="5033a-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="5033a-116">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="5033a-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="5033a-117">[从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="5033a-118">如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="5033a-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="5033a-119">准备应用</span><span class="sxs-lookup"><span data-stu-id="5033a-119">Prepare the app</span></span>

<span data-ttu-id="5033a-120">现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="5033a-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="5033a-121">在应用的布局文件 (_Layout.cshtml) 中：</span><span class="sxs-lookup"><span data-stu-id="5033a-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="5033a-122">将以下 `<base>` 标记添加到 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="5033a-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="5033a-123">前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。</span><span class="sxs-lookup"><span data-stu-id="5033a-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="5033a-124">如果应用是子应用程序，请按照 <xref:host-and-deploy/blazor/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="5033a-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="5033a-125">_Layout.cshtml 文件位于 Razor Pages 应用的 Pages/Shared 文件夹中，或 MVC 应用的 Views/Shared 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5033a-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="5033a-126">紧接在 `</body>` 结束标记前面，添加 blazor.server.js 脚本的 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="5033a-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="5033a-127">框架会将 blazor.server.js 脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="5033a-128">无需手动将脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="5033a-129">将 _Imports.razor 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：</span><span class="sxs-lookup"><span data-stu-id="5033a-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="5033a-130">在 `Startup.ConfigureServices` 中，注册 Blazor 服务器服务：</span><span class="sxs-lookup"><span data-stu-id="5033a-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="5033a-131">在 `Startup.Configure` 中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：</span><span class="sxs-lookup"><span data-stu-id="5033a-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="5033a-132">将组件集成到任何页面或视图。</span><span class="sxs-lookup"><span data-stu-id="5033a-132">Integrate components into any page or view.</span></span> <span data-ttu-id="5033a-133">有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。</span><span class="sxs-lookup"><span data-stu-id="5033a-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="5033a-134">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="5033a-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="5033a-135">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5033a-136">在 Razor Pages 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="5033a-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="5033a-137">按照[准备应用](#prepare-the-app)部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="5033a-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5033a-138">将具有以下内容的 App.razor 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="5033a-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="5033a-139">将具有以下内容的 _Host.cshtml 文件添加到 Pages 文件夹：</span><span class="sxs-lookup"><span data-stu-id="5033a-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5033a-140">组件将共享 _Layout.cshtml 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="5033a-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="5033a-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="5033a-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5033a-142">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="5033a-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5033a-143">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="5033a-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5033a-144">呈现模式</span><span class="sxs-lookup"><span data-stu-id="5033a-144">Render Mode</span></span> | <span data-ttu-id="5033a-145">描述</span><span class="sxs-lookup"><span data-stu-id="5033a-145">Description</span></span> |
   | ---
<span data-ttu-id="5033a-146">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-148">'Blazor'</span></span>
- <span data-ttu-id="5033a-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-149">'Identity'</span></span>
- <span data-ttu-id="5033a-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-151">'Razor'</span></span>
- <span data-ttu-id="5033a-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-153">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-155">'Blazor'</span></span>
- <span data-ttu-id="5033a-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-156">'Identity'</span></span>
- <span data-ttu-id="5033a-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-158">'Razor'</span></span>
- <span data-ttu-id="5033a-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-160">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-162">'Blazor'</span></span>
- <span data-ttu-id="5033a-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-163">'Identity'</span></span>
- <span data-ttu-id="5033a-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-165">'Razor'</span></span>
- <span data-ttu-id="5033a-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-166">'SignalR' uid:</span></span> 

<span data-ttu-id="5033a-167">------ | --- title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-169">'Blazor'</span></span>
- <span data-ttu-id="5033a-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-170">'Identity'</span></span>
- <span data-ttu-id="5033a-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-172">'Razor'</span></span>
- <span data-ttu-id="5033a-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-174">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-176">'Blazor'</span></span>
- <span data-ttu-id="5033a-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-177">'Identity'</span></span>
- <span data-ttu-id="5033a-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-179">'Razor'</span></span>
- <span data-ttu-id="5033a-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-181">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-183">'Blazor'</span></span>
- <span data-ttu-id="5033a-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-184">'Identity'</span></span>
- <span data-ttu-id="5033a-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-186">'Razor'</span></span>
- <span data-ttu-id="5033a-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-187">'SignalR' uid:</span></span> 

<span data-ttu-id="5033a-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 在静态 HTML 中呈现 `App` 组件，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5033a-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5033a-189">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5033a-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5033a-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5033a-191">不包括 `App` 组件的输出。</span><span class="sxs-lookup"><span data-stu-id="5033a-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5033a-192">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5033a-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 在静态 HTML 中呈现 `App` 组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5033a-194">要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5033a-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5033a-195">在 `Startup.Configure` 中，将 _Host.cshtml 的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="5033a-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="5033a-196">将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-196">Add routable components to the app.</span></span> <span data-ttu-id="5033a-197">例如：</span><span class="sxs-lookup"><span data-stu-id="5033a-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5033a-198">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="5033a-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="5033a-199">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="5033a-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="5033a-200">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5033a-201">在 MVC 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="5033a-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="5033a-202">按照[准备应用](#prepare-the-app)部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="5033a-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5033a-203">将具有以下内容的 App.razor 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="5033a-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="5033a-204">将具有以下内容的 _Host.cshtml 文件添加到 Views/Home 文件夹：</span><span class="sxs-lookup"><span data-stu-id="5033a-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5033a-205">组件将共享 _Layout.cshtml 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="5033a-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="5033a-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="5033a-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5033a-207">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="5033a-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5033a-208">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="5033a-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5033a-209">呈现模式</span><span class="sxs-lookup"><span data-stu-id="5033a-209">Render Mode</span></span> | <span data-ttu-id="5033a-210">描述</span><span class="sxs-lookup"><span data-stu-id="5033a-210">Description</span></span> |
   | ---
<span data-ttu-id="5033a-211">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-213">'Blazor'</span></span>
- <span data-ttu-id="5033a-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-214">'Identity'</span></span>
- <span data-ttu-id="5033a-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-216">'Razor'</span></span>
- <span data-ttu-id="5033a-217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-218">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-220">'Blazor'</span></span>
- <span data-ttu-id="5033a-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-221">'Identity'</span></span>
- <span data-ttu-id="5033a-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-223">'Razor'</span></span>
- <span data-ttu-id="5033a-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-225">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-227">'Blazor'</span></span>
- <span data-ttu-id="5033a-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-228">'Identity'</span></span>
- <span data-ttu-id="5033a-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-230">'Razor'</span></span>
- <span data-ttu-id="5033a-231">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-231">'SignalR' uid:</span></span> 

<span data-ttu-id="5033a-232">------ | --- title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-234">'Blazor'</span></span>
- <span data-ttu-id="5033a-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-235">'Identity'</span></span>
- <span data-ttu-id="5033a-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-237">'Razor'</span></span>
- <span data-ttu-id="5033a-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-239">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-241">'Blazor'</span></span>
- <span data-ttu-id="5033a-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-242">'Identity'</span></span>
- <span data-ttu-id="5033a-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-244">'Razor'</span></span>
- <span data-ttu-id="5033a-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5033a-246">title:“将 ASP.NET Core Razor 组件集成到 Razor Pages 和 MVC 应用”author: description:“了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。”</span><span class="sxs-lookup"><span data-stu-id="5033a-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="5033a-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5033a-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5033a-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5033a-248">'Blazor'</span></span>
- <span data-ttu-id="5033a-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5033a-249">'Identity'</span></span>
- <span data-ttu-id="5033a-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5033a-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="5033a-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5033a-251">'Razor'</span></span>
- <span data-ttu-id="5033a-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5033a-252">'SignalR' uid:</span></span> 

<span data-ttu-id="5033a-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 在静态 HTML 中呈现 `App` 组件，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5033a-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5033a-254">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5033a-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="5033a-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5033a-256">不包括 `App` 组件的输出。</span><span class="sxs-lookup"><span data-stu-id="5033a-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5033a-257">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5033a-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 在静态 HTML 中呈现 `App` 组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5033a-259">要详细了解组件标记帮助程序，请查看 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5033a-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5033a-260">向主控制器添加操作：</span><span class="sxs-lookup"><span data-stu-id="5033a-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="5033a-261">在 `Startup.Configure` 中，将返回 _Host.cshtml 视图的控制器操作的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="5033a-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="5033a-262">创建 Pages 文件夹并将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="5033a-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="5033a-263">例如：</span><span class="sxs-lookup"><span data-stu-id="5033a-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5033a-264">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="5033a-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="5033a-265">从页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="5033a-265">Render components from a page or view</span></span>

<span data-ttu-id="5033a-266">本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。</span><span class="sxs-lookup"><span data-stu-id="5033a-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="5033a-267">若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="5033a-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="5033a-268">呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="5033a-268">Render stateful interactive components</span></span>

<span data-ttu-id="5033a-269">可以将有状态的交互式组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="5033a-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="5033a-270">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="5033a-270">When the page or view renders:</span></span>

* <span data-ttu-id="5033a-271">该组件通过页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="5033a-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="5033a-272">用于预呈现的初始组件状态丢失。</span><span class="sxs-lookup"><span data-stu-id="5033a-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="5033a-273">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="5033a-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="5033a-274">以下 Razor 页面将呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="5033a-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="5033a-275">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5033a-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="5033a-276">呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="5033a-276">Render noninteractive components</span></span>

<span data-ttu-id="5033a-277">在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="5033a-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="5033a-278">由于该组件是以静态方式呈现的，因此它不是交互式组件：</span><span class="sxs-lookup"><span data-stu-id="5033a-278">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

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

<span data-ttu-id="5033a-279">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5033a-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="5033a-280">组件命名空间</span><span class="sxs-lookup"><span data-stu-id="5033a-280">Component namespaces</span></span>

<span data-ttu-id="5033a-281">使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 _ViewImports.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="5033a-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5033a-282">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5033a-282">In the following example:</span></span>

* <span data-ttu-id="5033a-283">将 `MyAppNamespace` 更改为应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="5033a-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5033a-284">如果不使用名为 Components 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5033a-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="5033a-285">_ViewImports.cshtml 文件位于 Razor Pages 应用的 Pages 文件夹中，或是 MVC 应用的 Views 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="5033a-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5033a-286">有关详细信息，请参阅 <xref:blazor/components#import-components>。</span><span class="sxs-lookup"><span data-stu-id="5033a-286">For more information, see <xref:blazor/components#import-components>.</span></span>
