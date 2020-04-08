---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求以及有关 NavLink 组件的信息。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218890"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="247e8-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="247e8-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="247e8-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="247e8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="247e8-105">了解如何路由请求以及如何使用 `NavLink` 组件在 Blazor 应用中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="247e8-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="247e8-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="247e8-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="247e8-107">Blazor Server 已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="247e8-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="247e8-108">ASP.NET Core 应用配置为接受 `MapBlazorHub` 中带有 `Startup.Configure` 的交互式组件的传入连接：</span><span class="sxs-lookup"><span data-stu-id="247e8-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="247e8-109">最典型的配置是将所有请求路由到 Razor 页面，该页面充当 Blazor Server 应用服务器端部分的主机。</span><span class="sxs-lookup"><span data-stu-id="247e8-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="247e8-110">按照约定，*主机*页通常命名为 *_Host.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="247e8-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="247e8-111">主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以较低的优先级运行。</span><span class="sxs-lookup"><span data-stu-id="247e8-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="247e8-112">其他路由不匹配时，会考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="247e8-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="247e8-113">这让应用能够使用其他控制器和页面，而不会干扰 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="247e8-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="247e8-114">路由模板</span><span class="sxs-lookup"><span data-stu-id="247e8-114">Route templates</span></span>

<span data-ttu-id="247e8-115">`Router` 组件可实现到具有指定路由的每个组件的路由。</span><span class="sxs-lookup"><span data-stu-id="247e8-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="247e8-116">`Router` 组件出现在 *App.razor* 文件中：</span><span class="sxs-lookup"><span data-stu-id="247e8-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="247e8-117">编译带有 *指令的*.razor`@page` 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Components.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="247e8-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="247e8-118">在运行时，`RouteView` 组件：</span><span class="sxs-lookup"><span data-stu-id="247e8-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="247e8-119">从 `RouteData` 接收 `Router` 以及任何所需的参数。</span><span class="sxs-lookup"><span data-stu-id="247e8-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="247e8-120">通过指定参数使用指定组件的布局（或可选的默认布局）呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="247e8-121">可选择使用布局类指定 `DefaultLayout` 参数，以用于未指定布局的组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="247e8-122">默认的 Blazor 模板指定 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="247e8-123">*MainLayout.razor* 在模板项目的 *Shared* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="247e8-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="247e8-124">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="247e8-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="247e8-125">可将多个路由模板应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="247e8-126">以下组件响应对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="247e8-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="247e8-127">若要正确解析 URL，应用必须在其 `<base>`wwwroot/index.html*文件 (Blazor WebAssembly) 或*Pages/_Host.cshtml*文件 (Blazor Server) 中包括* 标记，并使用 `href` 属性 (`<base href="/">`) 中指定的应用基路径。</span><span class="sxs-lookup"><span data-stu-id="247e8-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="247e8-128">有关更多信息，请参见 <xref:host-and-deploy/blazor/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="247e8-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="247e8-129">在找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="247e8-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="247e8-130">如果找不到所请求路由的内容，则 `Router` 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="247e8-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="247e8-131">在 *App.razor* 文件中，在 `NotFound` 组件的 `Router` 模板参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="247e8-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="247e8-132">`<NotFound>` 标记的内容可以包括任意项，例如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="247e8-133">若要将默认布局应用于 `NotFound` 内容，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="247e8-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="247e8-134">从多个程序集路由到组件</span><span class="sxs-lookup"><span data-stu-id="247e8-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="247e8-135">使用 `AdditionalAssemblies` 参数为 `Router` 组件指定搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="247e8-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="247e8-136">除 `AppAssembly` 指定的程序集外，还要考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="247e8-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="247e8-137">在以下示例中，`Component1` 是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="247e8-138">以下 `AdditionalAssemblies` 示例为 `Component1` 提供路由支持：</span><span class="sxs-lookup"><span data-stu-id="247e8-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="247e8-139">路由参数</span><span class="sxs-lookup"><span data-stu-id="247e8-139">Route parameters</span></span>

<span data-ttu-id="247e8-140">路由器使用路由参数以相同的名称填充相应的组件参数（不区分大小写）：</span><span class="sxs-lookup"><span data-stu-id="247e8-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="247e8-141">不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="247e8-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="247e8-142">上一个示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="247e8-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="247e8-143">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="247e8-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="247e8-144">第二个 `@page` 指令采用 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="247e8-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="247e8-145">路由约束</span><span class="sxs-lookup"><span data-stu-id="247e8-145">Route constraints</span></span>

<span data-ttu-id="247e8-146">路由约束强制在路由段和组件之间进行类型匹配。</span><span class="sxs-lookup"><span data-stu-id="247e8-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="247e8-147">在以下示例中，到 `Users` 组件的路由仅在以下情况下匹配：</span><span class="sxs-lookup"><span data-stu-id="247e8-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="247e8-148">请求 URL 上存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="247e8-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="247e8-149">`Id` 段是整数 (`int`)。</span><span class="sxs-lookup"><span data-stu-id="247e8-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="247e8-150">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="247e8-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="247e8-151">有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="247e8-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="247e8-152">约束</span><span class="sxs-lookup"><span data-stu-id="247e8-152">Constraint</span></span> | <span data-ttu-id="247e8-153">示例</span><span class="sxs-lookup"><span data-stu-id="247e8-153">Example</span></span>           | <span data-ttu-id="247e8-154">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="247e8-154">Example Matches</span></span>                                                                  | <span data-ttu-id="247e8-155">固定条件</span><span class="sxs-lookup"><span data-stu-id="247e8-155">Invariant</span></span><br><span data-ttu-id="247e8-156">区域性</span><span class="sxs-lookup"><span data-stu-id="247e8-156">culture</span></span><br><span data-ttu-id="247e8-157">匹配</span><span class="sxs-lookup"><span data-stu-id="247e8-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="247e8-158">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="247e8-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="247e8-159">No</span><span class="sxs-lookup"><span data-stu-id="247e8-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="247e8-160">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="247e8-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="247e8-161">是</span><span class="sxs-lookup"><span data-stu-id="247e8-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="247e8-162">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="247e8-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="247e8-163">是</span><span class="sxs-lookup"><span data-stu-id="247e8-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="247e8-164">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="247e8-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="247e8-165">是</span><span class="sxs-lookup"><span data-stu-id="247e8-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="247e8-166">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="247e8-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="247e8-167">是</span><span class="sxs-lookup"><span data-stu-id="247e8-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="247e8-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="247e8-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="247e8-169">No</span><span class="sxs-lookup"><span data-stu-id="247e8-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="247e8-170">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="247e8-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="247e8-171">是</span><span class="sxs-lookup"><span data-stu-id="247e8-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="247e8-172">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="247e8-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="247e8-173">是</span><span class="sxs-lookup"><span data-stu-id="247e8-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="247e8-174">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="247e8-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="247e8-175">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="247e8-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="247e8-176">使用包含点的 URL 进行路由</span><span class="sxs-lookup"><span data-stu-id="247e8-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="247e8-177">在 Blazor Server 应用中， *_Host.cshtml* 中的默认路由为 `/` (`@page "/"`)。</span><span class="sxs-lookup"><span data-stu-id="247e8-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="247e8-178">包含点 (`.`) 的请求 URL 与默认路由不匹配，因为 URL 似乎在请求文件。</span><span class="sxs-lookup"><span data-stu-id="247e8-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="247e8-179">Blazor 应用针对不存在的静态文件返回 *404 - 未找到*响应。</span><span class="sxs-lookup"><span data-stu-id="247e8-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="247e8-180">若要使用包含点的路由，请使用以下路由模板配置 *_Host.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="247e8-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="247e8-181">`"/{**path}"` 模板包括：</span><span class="sxs-lookup"><span data-stu-id="247e8-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="247e8-182">双星号 *catch-all* 语法 (`**`)，用于捕获跨多个文件夹边界的路径，而无需编码正斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="247e8-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="247e8-183">`path` 路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="247e8-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="247e8-184">Razor 组件 ( *.razor*) `*`不/支持 `**`catch-all **参数语法 (**  )。</span><span class="sxs-lookup"><span data-stu-id="247e8-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="247e8-185">有关更多信息，请参见 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="247e8-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="247e8-186">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="247e8-186">NavLink component</span></span>

<span data-ttu-id="247e8-187">创建导航链接时，请使用 `NavLink` 组件代替 HTML 超链接元素 (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="247e8-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="247e8-188">`NavLink` 组件的行为方式类似于 `<a>` 元素，但它根据其 `active` 是否与当前 URL 匹配来切换 `href` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="247e8-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="247e8-189">`active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。</span><span class="sxs-lookup"><span data-stu-id="247e8-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="247e8-190">以下 `NavMenu` 组件创建[启动](https://getbootstrap.com/docs/)导航栏，该导航栏演示如何使用 `NavLink` 组件：</span><span class="sxs-lookup"><span data-stu-id="247e8-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="247e8-191">有两个 `NavLinkMatch` 选项可分配给 `Match` 元素的 `<NavLink>` 属性：</span><span class="sxs-lookup"><span data-stu-id="247e8-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="247e8-192">`NavLinkMatch.All` &ndash; `NavLink` 在与当前整个 URL 匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="247e8-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="247e8-193">`NavLinkMatch.Prefix`（*默认*）&ndash; `NavLink` 在与当前 URL 的任何前缀匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="247e8-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="247e8-194">在前面的示例中，主页 `NavLink` `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`active`）处接收 `https://localhost:5001/` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="247e8-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="247e8-195">当用户访问带有 `NavLink` 前缀的任何 URL（例如，`active` 和 `MyComponent`）时，第二个 `https://localhost:5001/MyComponent` 接收 `https://localhost:5001/MyComponent/AnotherSegment` 类。</span><span class="sxs-lookup"><span data-stu-id="247e8-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="247e8-196">其他 `NavLink` 组件属性会传递到呈现的定位标记。</span><span class="sxs-lookup"><span data-stu-id="247e8-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="247e8-197">在以下示例中，`NavLink` 组件包括 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="247e8-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="247e8-198">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="247e8-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="247e8-199">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="247e8-199">URI and navigation state helpers</span></span>

<span data-ttu-id="247e8-200">在 C# 代码中将 <xref:Microsoft.AspNetCore.Components.NavigationManager> 与 URI 和导航配合使用。</span><span class="sxs-lookup"><span data-stu-id="247e8-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="247e8-201">`NavigationManager` 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="247e8-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="247e8-202">成员</span><span class="sxs-lookup"><span data-stu-id="247e8-202">Member</span></span> | <span data-ttu-id="247e8-203">描述</span><span class="sxs-lookup"><span data-stu-id="247e8-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="247e8-204">URI</span><span class="sxs-lookup"><span data-stu-id="247e8-204">Uri</span></span> | <span data-ttu-id="247e8-205">获取当前绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="247e8-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="247e8-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="247e8-206">BaseUri</span></span> | <span data-ttu-id="247e8-207">获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。</span><span class="sxs-lookup"><span data-stu-id="247e8-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="247e8-208">通常，`BaseUri` 对应于 `href`wwwroot/index.html`<base>` (*WebAssembly) 或*Pages/_Host.cshtmlBlazor (*Server) 中文档的* 元素上的 Blazor 属性。</span><span class="sxs-lookup"><span data-stu-id="247e8-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="247e8-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="247e8-209">NavigateTo</span></span> | <span data-ttu-id="247e8-210">导航到指定 URI。</span><span class="sxs-lookup"><span data-stu-id="247e8-210">Navigates to the specified URI.</span></span> <span data-ttu-id="247e8-211">如果 `forceLoad` 为 `true`，则：</span><span class="sxs-lookup"><span data-stu-id="247e8-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="247e8-212">客户端路由会被绕过。</span><span class="sxs-lookup"><span data-stu-id="247e8-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="247e8-213">无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="247e8-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="247e8-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="247e8-214">LocationChanged</span></span> | <span data-ttu-id="247e8-215">导航位置更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="247e8-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="247e8-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="247e8-216">ToAbsoluteUri</span></span> | <span data-ttu-id="247e8-217">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="247e8-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="247e8-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="247e8-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="247e8-219">给定基 URI（例如，之前由 `GetBaseUri` 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="247e8-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="247e8-220">选择该按钮后，以下组件导航到应用的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="247e8-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="247e8-221">以下组件处理位置更改事件。</span><span class="sxs-lookup"><span data-stu-id="247e8-221">The following component handles a location changed event.</span></span> <span data-ttu-id="247e8-222">在框架调用 `HandleLocationChanged` 时，解除挂接 `Dispose` 方法。</span><span class="sxs-lookup"><span data-stu-id="247e8-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="247e8-223">解除挂接该方法可允许组件进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="247e8-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="247e8-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 可提供以下有关该事件的信息：</span><span class="sxs-lookup"><span data-stu-id="247e8-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="247e8-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="247e8-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="247e8-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; 如果为 `true`，则 Blazor 截获了浏览器中的导航。</span><span class="sxs-lookup"><span data-stu-id="247e8-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="247e8-227">如果为 `false`，则 [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) 导致发生导航。</span><span class="sxs-lookup"><span data-stu-id="247e8-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="247e8-228">要详细了解组件处置，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="247e8-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
