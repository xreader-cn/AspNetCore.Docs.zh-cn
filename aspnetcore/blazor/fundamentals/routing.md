---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求以及有关 NavLink 组件的信息。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: a83637bb2690407dac5994bb63c765fcfa97462f
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981890"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="d8835-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="d8835-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d8835-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8835-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8835-105">了解如何路由请求，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件在 Blazor 应用中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="d8835-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d8835-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="d8835-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d8835-107">Blazor Server 已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="d8835-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d8835-108">ASP.NET Core 应用配置为接受 `Startup.Configure` 中带有 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 的交互式组件的传入连接：</span><span class="sxs-lookup"><span data-stu-id="d8835-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d8835-109">最典型的配置是将所有请求路由到 Razor 页面，该页面充当 Blazor Server 应用的服务器端部分的主机。</span><span class="sxs-lookup"><span data-stu-id="d8835-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d8835-110">按照约定，“主机”页通常命名为 `_Host.cshtml`。</span><span class="sxs-lookup"><span data-stu-id="d8835-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="d8835-111">主机文件中指定的路由称为 *回退路由*，因为它在路由匹配中以较低的优先级运行。</span><span class="sxs-lookup"><span data-stu-id="d8835-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d8835-112">其他路由不匹配时，会考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="d8835-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d8835-113">这让应用能够使用其他控制器和页面，而不会干扰 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="d8835-113">This allows the app to use other controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="d8835-114">若要了解如何为非根 URL 服务器托管配置 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="d8835-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d8835-115">路由模板</span><span class="sxs-lookup"><span data-stu-id="d8835-115">Route templates</span></span>

<span data-ttu-id="d8835-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件可实现到具有指定路由的每个组件的路由。</span><span class="sxs-lookup"><span data-stu-id="d8835-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d8835-117"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件出现在 `App.razor` 文件中：</span><span class="sxs-lookup"><span data-stu-id="d8835-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="d8835-118">编译带有 `@page` 指令的 `.razor` 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Components.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="d8835-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d8835-119">在运行时，<xref:Microsoft.AspNetCore.Components.RouteView> 组件：</span><span class="sxs-lookup"><span data-stu-id="d8835-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="d8835-120">从 <xref:Microsoft.AspNetCore.Components.Routing.Router> 接收 <xref:Microsoft.AspNetCore.Components.RouteData> 以及任何所需的参数。</span><span class="sxs-lookup"><span data-stu-id="d8835-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="d8835-121">通过指定参数使用指定组件的布局（或可选的默认布局）呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d8835-122">可选择使用布局类指定 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 参数，以用于未指定布局的组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d8835-123">默认的 Blazor 模板指定 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d8835-124">`MainLayout.razor` 位于模板项目的 `Shared` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="d8835-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="d8835-125">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="d8835-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d8835-126">可将多个路由模板应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d8835-127">以下组件响应对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="d8835-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="d8835-128">若要正确解析 URL，应用必须在其 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中加入 `<base>` 标记，并在 `href` 属性 (`<base href="/">`) 中指定应用基路径。</span><span class="sxs-lookup"><span data-stu-id="d8835-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d8835-129">有关详细信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="d8835-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d8835-130">在找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="d8835-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d8835-131">如果找不到所请求路由的内容，则 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="d8835-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d8835-132">在 `App.razor` 文件中，在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="d8835-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="d8835-133">`<NotFound>` 标记的内容可以包括任意项，例如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d8835-134">若要将默认布局应用于 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="d8835-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d8835-135">从多个程序集路由到组件</span><span class="sxs-lookup"><span data-stu-id="d8835-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d8835-136">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 参数为 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件指定搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="d8835-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="d8835-137">除 `AppAssembly` 指定的程序集外，还要考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="d8835-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d8835-138">在以下示例中，`Component1` 是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d8835-139">以下 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 示例为 `Component1` 提供路由支持：</span><span class="sxs-lookup"><span data-stu-id="d8835-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="d8835-140">路由参数</span><span class="sxs-lookup"><span data-stu-id="d8835-140">Route parameters</span></span>

<span data-ttu-id="d8835-141">路由器使用路由参数以相同的名称填充相应的组件参数（不区分大小写）。</span><span class="sxs-lookup"><span data-stu-id="d8835-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d8835-142">支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="d8835-142">Optional parameters are supported.</span></span> <span data-ttu-id="d8835-143">在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="d8835-143">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="d8835-144">如果该段不存在，则将 `Text` 的值设置为 `fantastic`：</span><span class="sxs-lookup"><span data-stu-id="d8835-144">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="d8835-145">不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="d8835-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="d8835-146">上一个示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="d8835-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d8835-147">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d8835-148">第二个 `@page` 指令采用 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="d8835-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="d8835-149">在 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) 上还不是在 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 上使用，以允许应用使用不同的可选参数值导航到同一组件。</span><span class="sxs-lookup"><span data-stu-id="d8835-149">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="d8835-150">根据上述示例，当用户应该能够从 `/RouteParameter` 导航到 `/RouteParameter/awesome` 或从 `/RouteParameter/awesome` 导航到 `/RouteParameter` 时使用 `OnParametersSet`：</span><span class="sxs-lookup"><span data-stu-id="d8835-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="d8835-151">路由约束</span><span class="sxs-lookup"><span data-stu-id="d8835-151">Route constraints</span></span>

<span data-ttu-id="d8835-152">路由约束强制在路由段和组件之间进行类型匹配。</span><span class="sxs-lookup"><span data-stu-id="d8835-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d8835-153">在以下示例中，到 `Users` 组件的路由仅在以下情况下匹配：</span><span class="sxs-lookup"><span data-stu-id="d8835-153">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d8835-154">请求 URL 上存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="d8835-154">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d8835-155">`Id` 段是整数 (`int`)。</span><span class="sxs-lookup"><span data-stu-id="d8835-155">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d8835-156">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="d8835-156">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d8835-157">有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="d8835-157">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d8835-158">约束</span><span class="sxs-lookup"><span data-stu-id="d8835-158">Constraint</span></span> | <span data-ttu-id="d8835-159">示例</span><span class="sxs-lookup"><span data-stu-id="d8835-159">Example</span></span>           | <span data-ttu-id="d8835-160">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="d8835-160">Example Matches</span></span>                                                                  | <span data-ttu-id="d8835-161">固定条件</span><span class="sxs-lookup"><span data-stu-id="d8835-161">Invariant</span></span><br><span data-ttu-id="d8835-162">区域性</span><span class="sxs-lookup"><span data-stu-id="d8835-162">culture</span></span><br><span data-ttu-id="d8835-163">匹配</span><span class="sxs-lookup"><span data-stu-id="d8835-163">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d8835-164">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d8835-164">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d8835-165">否</span><span class="sxs-lookup"><span data-stu-id="d8835-165">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d8835-166">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d8835-166">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d8835-167">是</span><span class="sxs-lookup"><span data-stu-id="d8835-167">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d8835-168">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d8835-168">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d8835-169">是</span><span class="sxs-lookup"><span data-stu-id="d8835-169">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d8835-170">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d8835-170">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d8835-171">是</span><span class="sxs-lookup"><span data-stu-id="d8835-171">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d8835-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d8835-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d8835-173">是</span><span class="sxs-lookup"><span data-stu-id="d8835-173">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d8835-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d8835-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d8835-175">否</span><span class="sxs-lookup"><span data-stu-id="d8835-175">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d8835-176">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d8835-176">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d8835-177">是</span><span class="sxs-lookup"><span data-stu-id="d8835-177">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d8835-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d8835-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d8835-179">是</span><span class="sxs-lookup"><span data-stu-id="d8835-179">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d8835-180">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 <xref:System.DateTime>）。</span><span class="sxs-lookup"><span data-stu-id="d8835-180">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="d8835-181">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="d8835-181">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d8835-182">使用包含点的 URL 进行路由</span><span class="sxs-lookup"><span data-stu-id="d8835-182">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d8835-183">对于托管的 Blazor WebAssembly 和 Blazor Server 应用，服务器端默认路由模板假定，如果请求 URL 的最后一段包含一个点 (`.`)，则请求一个文件（例如 `https://localhost.com:5001/example/some.thing`）。</span><span class="sxs-lookup"><span data-stu-id="d8835-183">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="d8835-184">在没有额外配置的情况下，应用将返回“404 - 未找到”响应（如果这将路由到组件）。</span><span class="sxs-lookup"><span data-stu-id="d8835-184">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="d8835-185">若要使用具有包含点的一个或多个参数的路由，则应用必须使用自定义模板配置该路由。</span><span class="sxs-lookup"><span data-stu-id="d8835-185">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="d8835-186">请考虑下面的 `Example` 组件，它可以从 URL 的最后一段接收路由参数：</span><span class="sxs-lookup"><span data-stu-id="d8835-186">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="d8835-187">若要允许托管的 Blazor WebAssembly 解决方案的服务器应用路由 `param` 参数中包含一个点的请求，请添加一个回退文件路由模板，在该模板的 `Startup.Configure` (`Startup.cs`) 中包含该可选参数：</span><span class="sxs-lookup"><span data-stu-id="d8835-187">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="d8835-188">若要配置 Blazor Server 应用以在 `param` 参数中使用一个点来路由请求，请添加一个回退页面路由模板，该模板具有 `Startup.Configure` (`Startup.cs`) 中的可选参数：</span><span class="sxs-lookup"><span data-stu-id="d8835-188">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="d8835-189">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="d8835-189">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="d8835-190">catch-all 路由参数</span><span class="sxs-lookup"><span data-stu-id="d8835-190">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d8835-191">*本部分应用于 .NET 5 候选发布 1 (RC1) 或更高版本中的 ASP.NET Core。*</span><span class="sxs-lookup"><span data-stu-id="d8835-191">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="d8835-192">组件支持可跨多个文件夹边界捕获路径的 catch-all 路由参数。</span><span class="sxs-lookup"><span data-stu-id="d8835-192">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="d8835-193">catch-all 路由参数必须满足以下条件：</span><span class="sxs-lookup"><span data-stu-id="d8835-193">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="d8835-194">以与路由段名称匹配的方式命名。</span><span class="sxs-lookup"><span data-stu-id="d8835-194">Named to match the route segment name.</span></span> <span data-ttu-id="d8835-195">命名不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d8835-195">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="d8835-196">`string` 类型。</span><span class="sxs-lookup"><span data-stu-id="d8835-196">A `string` type.</span></span> <span data-ttu-id="d8835-197">框架不提供自动强制转换。</span><span class="sxs-lookup"><span data-stu-id="d8835-197">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="d8835-198">位于 URL 的末尾。</span><span class="sxs-lookup"><span data-stu-id="d8835-198">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="d8835-199">对于具有 `/page/{*pageRoute}` 路由模板的 URL `/page/this/is/a/test`，`PageRoute` 的值设置为 `this/is/a/test`。</span><span class="sxs-lookup"><span data-stu-id="d8835-199">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="d8835-200">对捕获路径的斜杠和段进行解码。</span><span class="sxs-lookup"><span data-stu-id="d8835-200">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="d8835-201">对于 `/page/{*pageRoute}` 的路由模板，URL `/page/this/is/a%2Ftest%2A` 会生成 `this/is/a/test*`。</span><span class="sxs-lookup"><span data-stu-id="d8835-201">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d8835-202">ASP.NET Core 5.0 或更高版本中支持 catch-all 路由参数。</span><span class="sxs-lookup"><span data-stu-id="d8835-202">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="d8835-203">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="d8835-203">NavLink component</span></span>

<span data-ttu-id="d8835-204">创建导航链接时，请使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件代替 HTML 超链接元素 (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="d8835-204">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d8835-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件的行为方式类似于 `<a>` 元素，但它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="d8835-205">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d8835-206">`active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。</span><span class="sxs-lookup"><span data-stu-id="d8835-206">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="d8835-207">也可以选择将 CSS 类名分配到 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>，以便在当前路由与 `href` 匹配时将自定义 CSS 类应用到呈现的链接。</span><span class="sxs-lookup"><span data-stu-id="d8835-207">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="d8835-208">以下 `NavMenu` 组件创建 [`Bootstrap`](https://getbootstrap.com/docs/) 导航栏，该导航栏演示如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件：</span><span class="sxs-lookup"><span data-stu-id="d8835-208">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d8835-209">有两个 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 选项可分配给 `<NavLink>` 元素的 `Match` 属性：</span><span class="sxs-lookup"><span data-stu-id="d8835-209">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d8835-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前整个 URL 匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="d8835-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d8835-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>（默认）：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前 URL 的任何前缀匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="d8835-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d8835-212">在前面的示例中，主页 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`https://localhost:5001/`）处接收 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="d8835-212">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d8835-213">当用户访问带有 `MyComponent` 前缀的任何 URL（例如，`https://localhost:5001/MyComponent` 和 `https://localhost:5001/MyComponent/AnotherSegment`）时，第二个 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 接收 `active` 类。</span><span class="sxs-lookup"><span data-stu-id="d8835-213">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d8835-214">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件属性会传递到呈现的定位标记。</span><span class="sxs-lookup"><span data-stu-id="d8835-214">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d8835-215">在以下示例中，<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件包括 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="d8835-215">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d8835-216">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="d8835-216">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="d8835-217">由于 Blazor 呈现子内容的方式，如果在 `NavLink`（子）组件的内容中使用递增循环变量，则在 `for` 循环内呈现 `NavLink` 组件需要本地索引变量：</span><span class="sxs-lookup"><span data-stu-id="d8835-217">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="d8835-218">在[子内容](xref:blazor/components/index#child-content)中使用循环变量的任何子组件（而不仅仅是 `NavLink` 组件）都要求必须在此方案中使用索引变量。</span><span class="sxs-lookup"><span data-stu-id="d8835-218">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="d8835-219">或者，将 `foreach` 循环用于 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="d8835-219">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d8835-220">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="d8835-220">URI and navigation state helpers</span></span>

<span data-ttu-id="d8835-221">在 C# 代码中将 <xref:Microsoft.AspNetCore.Components.NavigationManager> 与 URI 和导航配合使用。</span><span class="sxs-lookup"><span data-stu-id="d8835-221">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d8835-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="d8835-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d8835-223">成员</span><span class="sxs-lookup"><span data-stu-id="d8835-223">Member</span></span> | <span data-ttu-id="d8835-224">描述</span><span class="sxs-lookup"><span data-stu-id="d8835-224">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="d8835-225">获取当前绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="d8835-225">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="d8835-226">获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。</span><span class="sxs-lookup"><span data-stu-id="d8835-226">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d8835-227">通常，<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 对应于 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 中文档的 `<base>` 元素上的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="d8835-227">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="d8835-228">导航到指定 URI。</span><span class="sxs-lookup"><span data-stu-id="d8835-228">Navigates to the specified URI.</span></span> <span data-ttu-id="d8835-229">如果 `forceLoad` 为 `true`，则：</span><span class="sxs-lookup"><span data-stu-id="d8835-229">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d8835-230">客户端路由会被绕过。</span><span class="sxs-lookup"><span data-stu-id="d8835-230">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d8835-231">无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="d8835-231">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="d8835-232">导航位置更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="d8835-232">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="d8835-233">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="d8835-233">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="d8835-234">给定基 URI（例如，之前由 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="d8835-234">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d8835-235">选择该按钮后，以下组件导航到应用的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="d8835-235">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="d8835-236">以下组件通过订阅 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 来处理位置改变事件。</span><span class="sxs-lookup"><span data-stu-id="d8835-236">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d8835-237">在框架调用 `Dispose` 时，解除挂接 `HandleLocationChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="d8835-237">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d8835-238">解除挂接该方法可允许组件进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="d8835-238">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="d8835-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 可提供以下有关该事件的信息：</span><span class="sxs-lookup"><span data-stu-id="d8835-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="d8835-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="d8835-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="d8835-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果为 `true`，则 Blazor 拦截了浏览器中的导航。</span><span class="sxs-lookup"><span data-stu-id="d8835-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d8835-242">如果为 `false`，则 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 导致了导航发生。</span><span class="sxs-lookup"><span data-stu-id="d8835-242">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="d8835-243">要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="d8835-243">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="d8835-244">查询字符串和分析参数</span><span class="sxs-lookup"><span data-stu-id="d8835-244">Query string and parse parameters</span></span>

<span data-ttu-id="d8835-245">可以从 <xref:Microsoft.AspNetCore.Components.NavigationManager> 的 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> 属性中获取请求的查询字符串：</span><span class="sxs-lookup"><span data-stu-id="d8835-245">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="d8835-246">若要分析查询字符串的参数，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d8835-246">To parse a query string's parameters:</span></span>

* <span data-ttu-id="d8835-247">为 [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="d8835-247">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="d8835-248">在使用 <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> 分析查询字符串后获取值。</span><span class="sxs-lookup"><span data-stu-id="d8835-248">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="d8835-249">前面示例中的占位符 `{KEY}` 是查询字符串参数键。</span><span class="sxs-lookup"><span data-stu-id="d8835-249">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="d8835-250">例如，URL 键值对 `?ship=Tardis` 使用键 `ship`。</span><span class="sxs-lookup"><span data-stu-id="d8835-250">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
