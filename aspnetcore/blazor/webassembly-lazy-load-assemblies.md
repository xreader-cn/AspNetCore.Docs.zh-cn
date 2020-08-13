---
title: 在 ASP.NET Core Blazor WebAssembly 中延迟加载程序集
author: guardrex
description: 了解如何在 ASP.NET Core Blazor WebAssembly 应用中延迟加载程序集。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0ce03badccad4e06aa3c316580ab82be38a806c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013367"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="7787d-103">在 ASP.NET Core Blazor WebAssembly 中延迟加载程序集</span><span class="sxs-lookup"><span data-stu-id="7787d-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7787d-104">作者：[Safia Abdalla](https://safia.rocks) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7787d-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7787d-105">通过推迟某些应用程序程序集的加载，直到需要时才加载，来提高 Blazor WebAssembly 应用启动性能，这种方式称为“延迟加载”。</span><span class="sxs-lookup"><span data-stu-id="7787d-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="7787d-106">例如，只有在用户导航到某个组件时，才将用于呈现该组件的程序集设置为加载。</span><span class="sxs-lookup"><span data-stu-id="7787d-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="7787d-107">加载后，程序集被缓存在客户端，供以后的所有导航使用。</span><span class="sxs-lookup"><span data-stu-id="7787d-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="7787d-108">使用 Blazor 的延迟加载功能，可以将应用程序集标记为延迟加载，当用户导航到特定路由时，将在运行时加载程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="7787d-109">此功能包含对项目文件的更改和对应用程序的路由器的更改。</span><span class="sxs-lookup"><span data-stu-id="7787d-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="7787d-110">由于程序集不会被下载到 Blazor Server 应用中的客户端，因此程序集的延迟加载对 Blazor Server 应用并没有好处。</span><span class="sxs-lookup"><span data-stu-id="7787d-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="7787d-111">项目文件</span><span class="sxs-lookup"><span data-stu-id="7787d-111">Project file</span></span>

<span data-ttu-id="7787d-112">使用 `BlazorWebAssemblyLazyLoad` 项标记应用的项目文件 (`.csproj`) 中用于延迟加载的程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="7787d-113">使用不带 `.dll` 扩展名的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="7787d-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="7787d-114">Blazor 框架可防止在应用启动时加载由此项组指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="7787d-115">下面的示例将一个大型自定义程序集 (`GrantImaharaRobotControls.dll`) 标记为进行延迟加载。</span><span class="sxs-lookup"><span data-stu-id="7787d-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="7787d-116">如果标记为进行延迟加载的程序集具有依赖项，还必须在项目文件中同时将这些依赖项标记为延迟加载。</span><span class="sxs-lookup"><span data-stu-id="7787d-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="7787d-117">只能延迟加载应用使用的程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="7787d-118">链接器可以从已发布的输出中去除未使用的程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="7787d-119">`Router` 组件</span><span class="sxs-lookup"><span data-stu-id="7787d-119">`Router` component</span></span>

<span data-ttu-id="7787d-120">Blazor 的 `Router` 组件指定哪个程序集 Blazor 搜索可路由组件。</span><span class="sxs-lookup"><span data-stu-id="7787d-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="7787d-121">`Router` 组件还负责为用户导航的路由呈现组件。</span><span class="sxs-lookup"><span data-stu-id="7787d-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="7787d-122">`Router` 组件支持 `OnNavigateAsync` 功能，该功能可与延迟加载一起使用。</span><span class="sxs-lookup"><span data-stu-id="7787d-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="7787d-123">在应用的 `Router` 组件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="7787d-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="7787d-124">添加一个 `OnNavigateAsync` 回调。</span><span class="sxs-lookup"><span data-stu-id="7787d-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="7787d-125">当用户执行以下操作时，将调用 `OnNavigateAsync` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="7787d-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="7787d-126">通过直接从自己的浏览器导航到某个路由，首次访问该路由。</span><span class="sxs-lookup"><span data-stu-id="7787d-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="7787d-127">使用链接或 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 调用导航到一个新路由。</span><span class="sxs-lookup"><span data-stu-id="7787d-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="7787d-128">如果延迟加载的程序集包含可路由的组件，则向组件添加一个 [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>>（例如，命名为 `lazyLoadedAssemblies`）。</span><span class="sxs-lookup"><span data-stu-id="7787d-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="7787d-129">如果程序集包含可路由的组件，程序集将被传递回 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。</span><span class="sxs-lookup"><span data-stu-id="7787d-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="7787d-130">框架会搜索程序集中的路由，并在发现任何新路由时更新路由集合。</span><span class="sxs-lookup"><span data-stu-id="7787d-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="7787d-131">如果 `OnNavigateAsync` 回调引发一个未处理的异常，则会调用 [Blazor 错误 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)。</span><span class="sxs-lookup"><span data-stu-id="7787d-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="7787d-132">`OnNavigateAsync` 中的程序集加载逻辑</span><span class="sxs-lookup"><span data-stu-id="7787d-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="7787d-133">`OnNavigateAsync` 有一个 `NavigationContext` 参数，它提供当前异步导航事件的相关信息，包括目标路径 (`Path`) 和取消标记 (`CancellationToken`)：</span><span class="sxs-lookup"><span data-stu-id="7787d-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="7787d-134">`Path` 属性是相对于应用基路径（如 `/robot`）的用户目标路径。</span><span class="sxs-lookup"><span data-stu-id="7787d-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="7787d-135">`CancellationToken` 可以用来观察异步任务的取消。</span><span class="sxs-lookup"><span data-stu-id="7787d-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="7787d-136">当用户导航到其他页时，`OnNavigateAsync` 会自动取消当前正在运行的导航任务。</span><span class="sxs-lookup"><span data-stu-id="7787d-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="7787d-137">在 `OnNavigateAsync` 内，实现逻辑以确定要加载的程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="7787d-138">选项包括：</span><span class="sxs-lookup"><span data-stu-id="7787d-138">Options include:</span></span>

* <span data-ttu-id="7787d-139">`OnNavigateAsync` 方法内部的条件检查。</span><span class="sxs-lookup"><span data-stu-id="7787d-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="7787d-140">一个将路由映射到程序集名称的查找表，可以注入到组件中，也可以在 [`@code`](xref:mvc/views/razor#code) 块内实现。</span><span class="sxs-lookup"><span data-stu-id="7787d-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="7787d-141">`LazyAssemblyLoader` 是一个框架提供的用于加载程序集的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="7787d-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="7787d-142">将 `LazyAssemblyLoader` 注入 `Router` 组件：</span><span class="sxs-lookup"><span data-stu-id="7787d-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="7787d-143">`LazyAssemblyLoader` 提供的 `LoadAssembliesAsync` 方法可以：</span><span class="sxs-lookup"><span data-stu-id="7787d-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="7787d-144">使用 JS 互操作通过网络调用来提取程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="7787d-145">在浏览器中将程序集加载到正在 WebAssembly 上执行的运行时。</span><span class="sxs-lookup"><span data-stu-id="7787d-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7787d-146">框架的延迟加载实现支持在服务器上预呈现。</span><span class="sxs-lookup"><span data-stu-id="7787d-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="7787d-147">在预呈现期间，所有的程序集（包括那些被标记为延迟加载的程序集）都被假定为已加载。</span><span class="sxs-lookup"><span data-stu-id="7787d-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="7787d-148">用户与 `<Navigating>` 内容的交互</span><span class="sxs-lookup"><span data-stu-id="7787d-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="7787d-149">加载程序集可能需要几秒钟的时间，`Router` 组件可以向用户指示正在发生页面转换：</span><span class="sxs-lookup"><span data-stu-id="7787d-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="7787d-150">为 <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 命名空间添加一条 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="7787d-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="7787d-151">向组件添加一个 `<Navigating>` 标记，其中包含在页面转换事件期间显示的标记。</span><span class="sxs-lookup"><span data-stu-id="7787d-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="7787d-152">处理 `OnNavigateAsync` 中的取消</span><span class="sxs-lookup"><span data-stu-id="7787d-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="7787d-153">传递到 `OnNavigateAsync` 回调的 `NavigationContext` 对象包含的 `CancellationToken` 在发生新导航事件时进行设置。</span><span class="sxs-lookup"><span data-stu-id="7787d-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="7787d-154">设置此取消标记时，`OnNavigateAsync` 回调必须引发，以避免在过时的导航中继续运行 `OnNavigateAsync` 回调。</span><span class="sxs-lookup"><span data-stu-id="7787d-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="7787d-155">如果用户导航到路由 A，然后立即路由到 B，则应用不应继续运行路由 A 的 `OnNavigateAsync` 回调：</span><span class="sxs-lookup"><span data-stu-id="7787d-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="7787d-156">如果取消 `NavigationContext` 中的取消标记会导致意外的行为（例如，呈现上一次导航中的组件），则不会引发。</span><span class="sxs-lookup"><span data-stu-id="7787d-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="7787d-157">完整示例</span><span class="sxs-lookup"><span data-stu-id="7787d-157">Complete example</span></span>

<span data-ttu-id="7787d-158">下面是完整的 `Router` 组件，它演示在用户导航到 `/robot` 时如何加载 `GrantImaharaRobotControls.dll` 程序集。</span><span class="sxs-lookup"><span data-stu-id="7787d-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="7787d-159">在页面转换期间，系统会向用户显示一条带样式的消息。</span><span class="sxs-lookup"><span data-stu-id="7787d-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="7787d-160">疑难解答</span><span class="sxs-lookup"><span data-stu-id="7787d-160">Troubleshoot</span></span>

* <span data-ttu-id="7787d-161">如果出现意外的呈现（例如，呈现上一次导航中的组件），请确认在设置取消标记时代码是否引发。</span><span class="sxs-lookup"><span data-stu-id="7787d-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="7787d-162">如果在应用程序启动时仍会加载程序集，请检查程序集是否在项目文件中被标记为延迟加载。</span><span class="sxs-lookup"><span data-stu-id="7787d-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7787d-163">其他资源</span><span class="sxs-lookup"><span data-stu-id="7787d-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
