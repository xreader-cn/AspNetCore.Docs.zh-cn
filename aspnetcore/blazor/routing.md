---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求以及有关 NavLink 组件的信息。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 85614acb9e76ac642e3ed2162aee3909349dd946
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105696"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="eac86-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="eac86-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="eac86-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eac86-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eac86-105">了解如何路由请求，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件在 Blazor 应用中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="eac86-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="eac86-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="eac86-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="eac86-107"> 服务器已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="eac86-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="eac86-108">ASP.NET Core 应用配置为接受 `Startup.Configure` 中带有 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 的交互式组件的传入连接：</span><span class="sxs-lookup"><span data-stu-id="eac86-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="eac86-109">最典型的配置是将所有请求路由到 Razor 页面，该页面为 Blazor 服务器应用充当服务器端的主机。</span><span class="sxs-lookup"><span data-stu-id="eac86-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="eac86-110">按照约定，*主机*页通常命名为 *_Host.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="eac86-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="eac86-111">主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以较低的优先级运行。</span><span class="sxs-lookup"><span data-stu-id="eac86-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="eac86-112">其他路由不匹配时，会考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="eac86-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="eac86-113">这让应用能够使用其他控制器和页面，而不会干扰 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="eac86-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="eac86-114">路由模板</span><span class="sxs-lookup"><span data-stu-id="eac86-114">Route templates</span></span>

<span data-ttu-id="eac86-115"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件可实现到具有指定路由的每个组件的路由。</span><span class="sxs-lookup"><span data-stu-id="eac86-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="eac86-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件出现在 *App.razor* 文件中：</span><span class="sxs-lookup"><span data-stu-id="eac86-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="eac86-117">编译带有 `@page` 指令的 *.razor* 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Components.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="eac86-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="eac86-118">在运行时，<xref:Microsoft.AspNetCore.Components.RouteView> 组件：</span><span class="sxs-lookup"><span data-stu-id="eac86-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="eac86-119">从 <xref:Microsoft.AspNetCore.Components.Routing.Router> 接收 <xref:Microsoft.AspNetCore.Components.RouteData> 以及任何所需的参数。</span><span class="sxs-lookup"><span data-stu-id="eac86-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="eac86-120">通过指定参数使用指定组件的布局（或可选的默认布局）呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="eac86-121">可选择使用布局类指定 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 参数，以用于未指定布局的组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="eac86-122">默认的 Blazor 模板指定 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="eac86-123">*MainLayout.razor* 在模板项目的 *Shared* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="eac86-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="eac86-124">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="eac86-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="eac86-125">可将多个路由模板应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="eac86-126">以下组件响应对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="eac86-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="eac86-127">若要正确解析 URL，应用必须在其 wwwroot/index.html 文件 (Blazor WebAssembly) 或 Pages/_Host.cshtml 文件 (Blazor Server) 中包括 `<base>` 标记，并在 `href` 属性 (`<base href="/">`) 中指定应用基路径。</span><span class="sxs-lookup"><span data-stu-id="eac86-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="eac86-128">有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="eac86-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="eac86-129">在找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="eac86-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="eac86-130">如果找不到所请求路由的内容，则 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="eac86-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="eac86-131">在 *App.razor* 文件中，在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="eac86-131">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="eac86-132">`<NotFound>` 标记的内容可以包括任意项，例如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="eac86-133">若要将默认布局应用于 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="eac86-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="eac86-134">从多个程序集路由到组件</span><span class="sxs-lookup"><span data-stu-id="eac86-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="eac86-135">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 参数为 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件指定搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="eac86-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="eac86-136">除 `AppAssembly` 指定的程序集外，还要考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="eac86-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="eac86-137">在以下示例中，`Component1` 是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="eac86-138">以下 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 示例为 `Component1` 提供路由支持：</span><span class="sxs-lookup"><span data-stu-id="eac86-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="eac86-139">路由参数</span><span class="sxs-lookup"><span data-stu-id="eac86-139">Route parameters</span></span>

<span data-ttu-id="eac86-140">路由器使用路由参数以相同的名称填充相应的组件参数（不区分大小写）：</span><span class="sxs-lookup"><span data-stu-id="eac86-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="eac86-141">不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="eac86-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="eac86-142">上一个示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="eac86-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="eac86-143">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="eac86-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="eac86-144">第二个 `@page` 指令采用 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="eac86-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="eac86-145">路由约束</span><span class="sxs-lookup"><span data-stu-id="eac86-145">Route constraints</span></span>

<span data-ttu-id="eac86-146">路由约束强制在路由段和组件之间进行类型匹配。</span><span class="sxs-lookup"><span data-stu-id="eac86-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="eac86-147">在以下示例中，到 `Users` 组件的路由仅在以下情况下匹配：</span><span class="sxs-lookup"><span data-stu-id="eac86-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="eac86-148">请求 URL 上存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="eac86-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="eac86-149">`Id` 段是整数 (`int`)。</span><span class="sxs-lookup"><span data-stu-id="eac86-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="eac86-150">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="eac86-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="eac86-151">有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="eac86-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="eac86-152">约束</span><span class="sxs-lookup"><span data-stu-id="eac86-152">Constraint</span></span> | <span data-ttu-id="eac86-153">示例</span><span class="sxs-lookup"><span data-stu-id="eac86-153">Example</span></span>           | <span data-ttu-id="eac86-154">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="eac86-154">Example Matches</span></span>                                                                  | <span data-ttu-id="eac86-155">固定条件</span><span class="sxs-lookup"><span data-stu-id="eac86-155">Invariant</span></span><br><span data-ttu-id="eac86-156">区域性</span><span class="sxs-lookup"><span data-stu-id="eac86-156">culture</span></span><br><span data-ttu-id="eac86-157">匹配</span><span class="sxs-lookup"><span data-stu-id="eac86-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="eac86-158">`true`，`FALSE`</span><span class="sxs-lookup"><span data-stu-id="eac86-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="eac86-159">否</span><span class="sxs-lookup"><span data-stu-id="eac86-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="eac86-160">`2016-12-31`，`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="eac86-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="eac86-161">是</span><span class="sxs-lookup"><span data-stu-id="eac86-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="eac86-162">`49.99`，`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="eac86-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="eac86-163">是</span><span class="sxs-lookup"><span data-stu-id="eac86-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="eac86-164">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="eac86-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="eac86-165">是</span><span class="sxs-lookup"><span data-stu-id="eac86-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="eac86-166">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="eac86-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="eac86-167">是</span><span class="sxs-lookup"><span data-stu-id="eac86-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="eac86-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`，`{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="eac86-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="eac86-169">否</span><span class="sxs-lookup"><span data-stu-id="eac86-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="eac86-170">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="eac86-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="eac86-171">是</span><span class="sxs-lookup"><span data-stu-id="eac86-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="eac86-172">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="eac86-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="eac86-173">是</span><span class="sxs-lookup"><span data-stu-id="eac86-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="eac86-174">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 <xref:System.DateTime>）。</span><span class="sxs-lookup"><span data-stu-id="eac86-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="eac86-175">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="eac86-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="eac86-176">使用包含点的 URL 进行路由</span><span class="sxs-lookup"><span data-stu-id="eac86-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="eac86-177">在 Blazor 服务器应用中，_Host.cshtml 中的默认路由为 `/` (`@page "/"`)。</span><span class="sxs-lookup"><span data-stu-id="eac86-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="eac86-178">包含点 (`.`) 的请求 URL 与默认路由不匹配，因为 URL 似乎在请求文件。</span><span class="sxs-lookup"><span data-stu-id="eac86-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="eac86-179">Blazor 应用针对不存在的静态文件返回“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="eac86-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="eac86-180">若要使用包含点的路由，请使用以下路由模板配置 *_Host.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="eac86-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="eac86-181">`"/{**path}"` 模板包括：</span><span class="sxs-lookup"><span data-stu-id="eac86-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="eac86-182">双星号 *catch-all* 语法 (`**`)，用于捕获跨多个文件夹边界的路径，而无需编码正斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="eac86-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="eac86-183">`path` 路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="eac86-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="eac86-184">Razor 组件 (.razor) 不支持 Catch-all 参数语法 (`*`/`**`)。</span><span class="sxs-lookup"><span data-stu-id="eac86-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="eac86-185">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="eac86-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="eac86-186">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="eac86-186">NavLink component</span></span>

<span data-ttu-id="eac86-187">创建导航链接时，请使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件代替 HTML 超链接元素 (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="eac86-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="eac86-188"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件的行为方式类似于 `<a>` 元素，但它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="eac86-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="eac86-189">`active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。</span><span class="sxs-lookup"><span data-stu-id="eac86-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="eac86-190">以下 `NavMenu` 组件创建[启动](https://getbootstrap.com/docs/)导航栏，该导航栏演示如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件：</span><span class="sxs-lookup"><span data-stu-id="eac86-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="eac86-191">有两个 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 选项可分配给 `<NavLink>` 元素的 `Match` 属性：</span><span class="sxs-lookup"><span data-stu-id="eac86-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="eac86-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前整个 URL 匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="eac86-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="eac86-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>（默认）：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前 URL 的任何前缀匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="eac86-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="eac86-194">在前面的示例中，主页 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`https://localhost:5001/`）处接收 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="eac86-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="eac86-195">当用户访问带有 `MyComponent` 前缀的任何 URL（例如，`https://localhost:5001/MyComponent` 和 `https://localhost:5001/MyComponent/AnotherSegment`）时，第二个 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 接收 `active` 类。</span><span class="sxs-lookup"><span data-stu-id="eac86-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="eac86-196">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件属性会传递到呈现的定位标记。</span><span class="sxs-lookup"><span data-stu-id="eac86-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="eac86-197">在以下示例中，<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件包括 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="eac86-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="eac86-198">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="eac86-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="eac86-199">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="eac86-199">URI and navigation state helpers</span></span>

<span data-ttu-id="eac86-200">在 C# 代码中将 <xref:Microsoft.AspNetCore.Components.NavigationManager> 与 URI 和导航配合使用。</span><span class="sxs-lookup"><span data-stu-id="eac86-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="eac86-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="eac86-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="eac86-202">成员</span><span class="sxs-lookup"><span data-stu-id="eac86-202">Member</span></span> | <span data-ttu-id="eac86-203">描述</span><span class="sxs-lookup"><span data-stu-id="eac86-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="eac86-204">获取当前绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="eac86-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="eac86-205">获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。</span><span class="sxs-lookup"><span data-stu-id="eac86-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="eac86-206">通常，<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 对应于 *wwwroot/index.html* (Blazor WebAssembly) 或 *Pages/_Host.cshtml* (Blazor Server) 中文档的 `<base>` 元素上的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="eac86-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="eac86-207">导航到指定 URI。</span><span class="sxs-lookup"><span data-stu-id="eac86-207">Navigates to the specified URI.</span></span> <span data-ttu-id="eac86-208">如果 `forceLoad` 为 `true`，则：</span><span class="sxs-lookup"><span data-stu-id="eac86-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="eac86-209">客户端路由会被绕过。</span><span class="sxs-lookup"><span data-stu-id="eac86-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="eac86-210">无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="eac86-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="eac86-211">导航位置更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="eac86-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="eac86-212">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="eac86-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="eac86-213">给定基 URI（例如，之前由 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="eac86-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="eac86-214">选择该按钮后，以下组件导航到应用的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="eac86-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="eac86-215">以下组件通过设置 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 来处理位置改变事件。</span><span class="sxs-lookup"><span data-stu-id="eac86-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="eac86-216">在框架调用 `Dispose` 时，解除挂接 `HandleLocationChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="eac86-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="eac86-217">解除挂接该方法可允许组件进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="eac86-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="eac86-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 可提供以下有关该事件的信息：</span><span class="sxs-lookup"><span data-stu-id="eac86-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="eac86-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="eac86-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="eac86-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果为 `true`，则 Blazor 拦截了浏览器中的导航。</span><span class="sxs-lookup"><span data-stu-id="eac86-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="eac86-221">如果为 `false`，则 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 导致了导航发生。</span><span class="sxs-lookup"><span data-stu-id="eac86-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="eac86-222">要详细了解组件处置，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="eac86-222">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
