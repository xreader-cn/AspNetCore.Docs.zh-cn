---
title: 在 ASP.NET Core Blazor WebAssembly 中延迟加载程序集
author: guardrex
description: 了解如何在 ASP.NET Core Blazor WebAssembly 应用中延迟加载程序集。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6e7fa6e231e97793fbf7e1ac1d208bf3013c6fce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506562"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="36f97-103">在 ASP.NET Core Blazor WebAssembly 中延迟加载程序集</span><span class="sxs-lookup"><span data-stu-id="36f97-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="36f97-104">作者：[Safia Abdalla](https://safia.rocks) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="36f97-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="36f97-105">通过推迟某些应用程序程序集的加载，直到需要时才加载，来提高 Blazor WebAssembly 应用启动性能，这种方式称为“延迟加载”。</span><span class="sxs-lookup"><span data-stu-id="36f97-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="36f97-106">例如，只有在用户导航到某个组件时，才将用于呈现该组件的程序集设置为加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="36f97-107">加载后，程序集被缓存在客户端，供以后的所有导航使用。</span><span class="sxs-lookup"><span data-stu-id="36f97-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="36f97-108">使用 Blazor 的延迟加载功能，可以将应用程序集标记为延迟加载，当用户导航到特定路由时，将在运行时加载程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="36f97-109">此功能包含对项目文件的更改和对应用程序的路由器的更改。</span><span class="sxs-lookup"><span data-stu-id="36f97-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="36f97-110">由于程序集不会被下载到 Blazor Server 应用中的客户端，因此程序集的延迟加载对 Blazor Server 应用并没有好处。</span><span class="sxs-lookup"><span data-stu-id="36f97-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="36f97-111">项目文件</span><span class="sxs-lookup"><span data-stu-id="36f97-111">Project file</span></span>

<span data-ttu-id="36f97-112">使用 `BlazorWebAssemblyLazyLoad` 项标记应用的项目文件 (`.csproj`) 中用于延迟加载的程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="36f97-113">使用带 `.dll` 扩展名的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="36f97-113">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="36f97-114">Blazor 框架可防止在应用启动时加载由此项组指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="36f97-115">下面的示例将一个大型自定义程序集 (`GrantImaharaRobotControls.dll`) 标记为进行延迟加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="36f97-116">如果标记为进行延迟加载的程序集具有依赖项，还必须在项目文件中同时将这些依赖项标记为延迟加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="36f97-117">`Router` 组件</span><span class="sxs-lookup"><span data-stu-id="36f97-117">`Router` component</span></span>

<span data-ttu-id="36f97-118">Blazor 的 `Router` 组件指定哪个程序集 Blazor 搜索可路由组件。</span><span class="sxs-lookup"><span data-stu-id="36f97-118">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="36f97-119">`Router` 组件还负责为用户导航的路由呈现组件。</span><span class="sxs-lookup"><span data-stu-id="36f97-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="36f97-120">`Router` 组件支持 `OnNavigateAsync` 功能，该功能可与延迟加载一起使用。</span><span class="sxs-lookup"><span data-stu-id="36f97-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="36f97-121">在应用的 `Router` 组件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="36f97-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="36f97-122">添加一个 `OnNavigateAsync` 回调。</span><span class="sxs-lookup"><span data-stu-id="36f97-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="36f97-123">当用户执行以下操作时，将调用 `OnNavigateAsync` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="36f97-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="36f97-124">通过直接从自己的浏览器导航到某个路由，首次访问该路由。</span><span class="sxs-lookup"><span data-stu-id="36f97-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="36f97-125">使用链接或 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 调用导航到一个新路由。</span><span class="sxs-lookup"><span data-stu-id="36f97-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="36f97-126">如果延迟加载的程序集包含可路由的组件，则向组件添加一个 [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>>（例如，命名为 `lazyLoadedAssemblies`）。</span><span class="sxs-lookup"><span data-stu-id="36f97-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="36f97-127">如果程序集包含可路由的组件，程序集将被传递回 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。</span><span class="sxs-lookup"><span data-stu-id="36f97-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="36f97-128">框架会搜索程序集中的路由，并在发现任何新路由时更新路由集合。</span><span class="sxs-lookup"><span data-stu-id="36f97-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="36f97-129">如果 `OnNavigateAsync` 回调引发一个未处理的异常，则会调用 [Blazor 错误 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)。</span><span class="sxs-lookup"><span data-stu-id="36f97-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="36f97-130">`OnNavigateAsync` 中的程序集加载逻辑</span><span class="sxs-lookup"><span data-stu-id="36f97-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="36f97-131">`OnNavigateAsync` 有一个 `NavigationContext` 参数，它提供当前异步导航事件的相关信息，包括目标路径 (`Path`) 和取消标记 (`CancellationToken`)：</span><span class="sxs-lookup"><span data-stu-id="36f97-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="36f97-132">`Path` 属性是相对于应用基路径（如 `/robot`）的用户目标路径。</span><span class="sxs-lookup"><span data-stu-id="36f97-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="36f97-133">`CancellationToken` 可以用来观察异步任务的取消。</span><span class="sxs-lookup"><span data-stu-id="36f97-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="36f97-134">当用户导航到其他页时，`OnNavigateAsync` 会自动取消当前正在运行的导航任务。</span><span class="sxs-lookup"><span data-stu-id="36f97-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="36f97-135">在 `OnNavigateAsync` 内，实现逻辑以确定要加载的程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="36f97-136">选项包括：</span><span class="sxs-lookup"><span data-stu-id="36f97-136">Options include:</span></span>

* <span data-ttu-id="36f97-137">`OnNavigateAsync` 方法内部的条件检查。</span><span class="sxs-lookup"><span data-stu-id="36f97-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="36f97-138">一个将路由映射到程序集名称的查找表，可以注入到组件中，也可以在 [`@code`](xref:mvc/views/razor#code) 块内实现。</span><span class="sxs-lookup"><span data-stu-id="36f97-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="36f97-139">`LazyAssemblyLoader` 是一个框架提供的用于加载程序集的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="36f97-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="36f97-140">将 `LazyAssemblyLoader` 注入 `Router` 组件：</span><span class="sxs-lookup"><span data-stu-id="36f97-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="36f97-141">`LazyAssemblyLoader` 提供的 `LoadAssembliesAsync` 方法可以：</span><span class="sxs-lookup"><span data-stu-id="36f97-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="36f97-142">使用 JS 互操作通过网络调用来提取程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="36f97-143">在浏览器中将程序集加载到正在 WebAssembly 上执行的运行时。</span><span class="sxs-lookup"><span data-stu-id="36f97-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="36f97-144">框架的延迟加载实现支持在托管的 Blazor 解决方案中使用预呈现进行延迟加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="36f97-145">在预呈现期间，所有的程序集（包括那些被标记为延迟加载的程序集）都被假定为已加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="36f97-146">在服务器项目的 `Startup.ConfigureServices` 方法 (`Startup.cs`) 中手动注册 `LazyAssemblyLoader`：</span><span class="sxs-lookup"><span data-stu-id="36f97-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="36f97-147">用户与 `<Navigating>` 内容的交互</span><span class="sxs-lookup"><span data-stu-id="36f97-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="36f97-148">加载程序集可能需要几秒钟的时间，`Router` 组件可以向用户指示正在发生页面转换：</span><span class="sxs-lookup"><span data-stu-id="36f97-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="36f97-149">为 <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 命名空间添加一条 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="36f97-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="36f97-150">向组件添加一个 `<Navigating>` 标记，其中包含在页面转换事件期间显示的标记。</span><span class="sxs-lookup"><span data-stu-id="36f97-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="36f97-151">处理 `OnNavigateAsync` 中的取消</span><span class="sxs-lookup"><span data-stu-id="36f97-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="36f97-152">传递到 `OnNavigateAsync` 回调的 `NavigationContext` 对象包含的 `CancellationToken` 在发生新导航事件时进行设置。</span><span class="sxs-lookup"><span data-stu-id="36f97-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="36f97-153">设置此取消标记时，`OnNavigateAsync` 回调必须引发，以避免在过时的导航中继续运行 `OnNavigateAsync` 回调。</span><span class="sxs-lookup"><span data-stu-id="36f97-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="36f97-154">如果用户导航到路由 A，然后立即路由到 B，则应用不应继续运行路由 A 的 `OnNavigateAsync` 回调：</span><span class="sxs-lookup"><span data-stu-id="36f97-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> <span data-ttu-id="36f97-155">如果取消 `NavigationContext` 中的取消标记会导致意外的行为（例如，呈现上一次导航中的组件），则不会引发。</span><span class="sxs-lookup"><span data-stu-id="36f97-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="36f97-156">`OnNavigateAsync` 事件和重命名的程序集文件</span><span class="sxs-lookup"><span data-stu-id="36f97-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="36f97-157">资源加载程序依赖于在 `blazor.boot.json` 文件中定义的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="36f97-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="36f97-158">如果[程序集已重命名](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)，则 `OnNavigateAsync` 方法中使用的程序集名称和 `blazor.boot.json` 文件中的程序集名称将不同步。</span><span class="sxs-lookup"><span data-stu-id="36f97-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="36f97-159">若要更正此问题，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="36f97-159">To rectify this:</span></span>

* <span data-ttu-id="36f97-160">确定要使用的程序集名称时，检查应用是否在生产环境中运行。</span><span class="sxs-lookup"><span data-stu-id="36f97-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="36f97-161">将重命名的程序集名称存储在单独的文件中，并从该文件中读取，以确定要在 `LazyLoadAssemblyService` 和 `OnNavigateAsync` 方法中使用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="36f97-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="36f97-162">完整示例</span><span class="sxs-lookup"><span data-stu-id="36f97-162">Complete example</span></span>

<span data-ttu-id="36f97-163">下面是完整的 `Router` 组件，它演示在用户导航到 `/robot` 时如何加载 `GrantImaharaRobotControls.dll` 程序集。</span><span class="sxs-lookup"><span data-stu-id="36f97-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="36f97-164">在页面转换期间，系统会向用户显示一条带样式的消息。</span><span class="sxs-lookup"><span data-stu-id="36f97-164">During page transitions, a styled message is displayed to the user.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a><span data-ttu-id="36f97-165">疑难解答</span><span class="sxs-lookup"><span data-stu-id="36f97-165">Troubleshoot</span></span>

* <span data-ttu-id="36f97-166">如果出现意外的呈现（例如，呈现上一次导航中的组件），请确认在设置取消标记时代码是否引发。</span><span class="sxs-lookup"><span data-stu-id="36f97-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="36f97-167">如果在应用程序启动时仍会加载程序集，请检查程序集是否在项目文件中被标记为延迟加载。</span><span class="sxs-lookup"><span data-stu-id="36f97-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36f97-168">其他资源</span><span class="sxs-lookup"><span data-stu-id="36f97-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
