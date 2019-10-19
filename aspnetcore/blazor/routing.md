---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求，以及如何在 NavLink 组件中路由请求。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/routing
ms.openlocfilehash: d4b76c00f79f333884fa7e30b27eadc6e36de287
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589939"
---
# <a name="aspnet-core-opno-locblazor-routing"></a><span data-ttu-id="de7dd-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="de7dd-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="de7dd-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de7dd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="de7dd-105">了解如何路由请求，以及如何使用 `NavLink` 组件在 Blazor 应用程序中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="de7dd-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="de7dd-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="de7dd-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="de7dd-107"> Server 集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="de7dd-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="de7dd-108">ASP.NET Core 应用配置为接受 `Startup.Configure` 中 `MapBlazorHub` 的交互式组件的传入连接：</span><span class="sxs-lookup"><span data-stu-id="de7dd-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="de7dd-109">最典型的配置是将所有请求路由到 Razor 页，该页面充当 Blazor Server 应用程序的服务器端部分的主机。</span><span class="sxs-lookup"><span data-stu-id="de7dd-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="de7dd-110">按照约定，*主机*页通常名为 *_Host*。</span><span class="sxs-lookup"><span data-stu-id="de7dd-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="de7dd-111">主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以低优先级操作。</span><span class="sxs-lookup"><span data-stu-id="de7dd-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="de7dd-112">当其他路由不匹配时，将考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="de7dd-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="de7dd-113">这允许应用使用其他控制器和页面，而不会干扰 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="de7dd-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="de7dd-114">路由模板</span><span class="sxs-lookup"><span data-stu-id="de7dd-114">Route templates</span></span>

<span data-ttu-id="de7dd-115">@No__t_0 组件允许路由到具有指定路由的每个组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="de7dd-116">@No__t_0 组件显示在*app.config*文件中：</span><span class="sxs-lookup"><span data-stu-id="de7dd-116">The `Router` component appears in the *App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="de7dd-117">编译带有 `@page` 指令的*razor*文件时，将提供生成的类 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板。</span><span class="sxs-lookup"><span data-stu-id="de7dd-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="de7dd-118">在运行时，`RouteView` 组件：</span><span class="sxs-lookup"><span data-stu-id="de7dd-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="de7dd-119">接收来自 `Router` 的 `RouteData` 以及任何所需的参数。</span><span class="sxs-lookup"><span data-stu-id="de7dd-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="de7dd-120">使用指定的参数呈现指定组件及其布局（或可选默认布局）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="de7dd-121">您可以选择指定一个包含布局类的 `DefaultLayout` 参数，以用于未指定布局的组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="de7dd-122">默认 Blazor 模板指定 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="de7dd-123">*MainLayout*位于模板项目的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="de7dd-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="de7dd-124">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="de7dd-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="de7dd-125">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="de7dd-126">以下组件响应 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="de7dd-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="de7dd-127">若要正确解析 Url，应用必须在其*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/_Host*文件（Blazor 服务器）中包含一个 `<base>` 标记，该文件具有 `href` 属性（`<base href="/">`）中指定的应用程序基路径。</span><span class="sxs-lookup"><span data-stu-id="de7dd-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="de7dd-128">有关更多信息，请参见<xref:host-and-deploy/blazor/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="de7dd-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="de7dd-129">当找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="de7dd-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="de7dd-130">如果找不到请求的路由的内容，则 `Router` 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="de7dd-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="de7dd-131">在*app.config*文件中，设置 `Router` 组件的 `NotFound` 模板参数中的自定义内容：</span><span class="sxs-lookup"><span data-stu-id="de7dd-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```cshtml
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

<span data-ttu-id="de7dd-132">@No__t_0 标记的内容可以包括任意项，如其他交互组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="de7dd-133">若要将默认布局应用于 `NotFound` 内容，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="de7dd-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="de7dd-134">从多个程序集中路由到组件</span><span class="sxs-lookup"><span data-stu-id="de7dd-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="de7dd-135">使用 `AdditionalAssemblies` 参数为 `Router` 组件指定在搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="de7dd-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="de7dd-136">除了 `AppAssembly` 指定的程序集之外，还将考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="de7dd-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="de7dd-137">在下面的示例中，`Component1` 是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="de7dd-138">以下 `AdditionalAssemblies` 示例将导致对 `Component1` 的路由支持：</span><span class="sxs-lookup"><span data-stu-id="de7dd-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="de7dd-139">路由参数</span><span class="sxs-lookup"><span data-stu-id="de7dd-139">Route parameters</span></span>

<span data-ttu-id="de7dd-140">路由器使用路由参数来填充具有相同名称（不区分大小写）的相应组件参数：</span><span class="sxs-lookup"><span data-stu-id="de7dd-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="de7dd-141">ASP.NET Core 3.0 中的 Blazor 应用不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="de7dd-141">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0.</span></span> <span data-ttu-id="de7dd-142">前面的示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="de7dd-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="de7dd-143">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="de7dd-144">第二个 `@page` 指令采用 `{text}` 路由参数，并将该值分配给 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="de7dd-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="de7dd-145">路由约束</span><span class="sxs-lookup"><span data-stu-id="de7dd-145">Route constraints</span></span>

<span data-ttu-id="de7dd-146">路由约束向组件强制执行对路由段的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="de7dd-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="de7dd-147">在下面的示例中，路由到 `Users` 组件仅在以下情况下才匹配：</span><span class="sxs-lookup"><span data-stu-id="de7dd-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="de7dd-148">请求 URL 上存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="de7dd-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="de7dd-149">@No__t_0 段是一个整数（`int`）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-149">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="de7dd-150">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="de7dd-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="de7dd-151">有关与固定区域性匹配的路由约束，请参阅表下面的警告以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="de7dd-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="de7dd-152">约束</span><span class="sxs-lookup"><span data-stu-id="de7dd-152">Constraint</span></span> | <span data-ttu-id="de7dd-153">示例</span><span class="sxs-lookup"><span data-stu-id="de7dd-153">Example</span></span>           | <span data-ttu-id="de7dd-154">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="de7dd-154">Example Matches</span></span>                                                                  | <span data-ttu-id="de7dd-155">固定条件</span><span class="sxs-lookup"><span data-stu-id="de7dd-155">Invariant</span></span><br><span data-ttu-id="de7dd-156">区域性</span><span class="sxs-lookup"><span data-stu-id="de7dd-156">culture</span></span><br><span data-ttu-id="de7dd-157">匹配</span><span class="sxs-lookup"><span data-stu-id="de7dd-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="de7dd-158">`true`，`FALSE`</span><span class="sxs-lookup"><span data-stu-id="de7dd-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="de7dd-159">No</span><span class="sxs-lookup"><span data-stu-id="de7dd-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="de7dd-160">`2016-12-31`，`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="de7dd-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="de7dd-161">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="de7dd-162">`49.99`，`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="de7dd-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="de7dd-163">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="de7dd-164">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="de7dd-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="de7dd-165">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="de7dd-166">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="de7dd-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="de7dd-167">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="de7dd-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`，`{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="de7dd-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="de7dd-169">No</span><span class="sxs-lookup"><span data-stu-id="de7dd-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="de7dd-170">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="de7dd-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="de7dd-171">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="de7dd-172">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="de7dd-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="de7dd-173">是</span><span class="sxs-lookup"><span data-stu-id="de7dd-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="de7dd-174">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="de7dd-175">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="de7dd-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="de7dd-176">带有包含点的 Url 的路由</span><span class="sxs-lookup"><span data-stu-id="de7dd-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="de7dd-177">在 Blazor Server apps 中， *_Host*中的默认路由 `/` （`@page "/"`）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="de7dd-178">默认路由不会匹配包含点（`.`）的请求 URL，因为 URL 显示为请求文件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="de7dd-179">对于不存在的静态文件，Blazor 应用返回 " *404-未找到*" 响应。</span><span class="sxs-lookup"><span data-stu-id="de7dd-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="de7dd-180">若要使用包含点的路由，请使用以下路由模板配置 *_Host* ：</span><span class="sxs-lookup"><span data-stu-id="de7dd-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="de7dd-181">@No__t_0 模板包括：</span><span class="sxs-lookup"><span data-stu-id="de7dd-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="de7dd-182">双星号*catch-all*语法（`**`）捕获跨多个文件夹边界的路径，而无需编码正斜杠（`/`）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="de7dd-183">@No__t_0 路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="de7dd-183">A `path` route parameter name.</span></span>

<span data-ttu-id="de7dd-184">有关更多信息，请参见<xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="de7dd-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="de7dd-185">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="de7dd-185">NavLink component</span></span>

<span data-ttu-id="de7dd-186">创建导航链接时，请使用 `NavLink` 组件来代替 HTML hyperlink 元素（`<a>`）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-186">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="de7dd-187">@No__t_0 组件的行为类似于 `<a>` 元素，只不过它会根据其 `href` 是否匹配当前 URL 来切换 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="de7dd-187">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="de7dd-188">@No__t_0 类可帮助用户了解在显示的导航链接中哪一页是活动页。</span><span class="sxs-lookup"><span data-stu-id="de7dd-188">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="de7dd-189">以下 `NavMenu` 组件创建一个演示如何使用 `NavLink` 组件的[启动](https://getbootstrap.com/docs/)导航栏：</span><span class="sxs-lookup"><span data-stu-id="de7dd-189">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="de7dd-190">可以将两个 `NavLinkMatch` 选项分配给 `<NavLink>` 元素的 `Match` 属性：</span><span class="sxs-lookup"><span data-stu-id="de7dd-190">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="de7dd-191">如果 `NavLink` 在与整个当前 URL 匹配时处于活动状态，则 `NavLinkMatch.All` &ndash;。</span><span class="sxs-lookup"><span data-stu-id="de7dd-191">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="de7dd-192">`NavLinkMatch.Prefix` （*默认值*），当 `NavLink` 与当前 URL 的任何前缀匹配时，&ndash; 处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="de7dd-192">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="de7dd-193">在前面的示例中，Home `NavLink` `href=""` 与 home URL 匹配，并且仅接收应用的默认基路径 URL （例如 `https://localhost:5001/`）中的 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="de7dd-193">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="de7dd-194">第二个 `NavLink` 在用户访问具有 `MyComponent` 前缀的任何 URL （例如 `https://localhost:5001/MyComponent` 和 `https://localhost:5001/MyComponent/AnotherSegment`）时接收 `active` 类。</span><span class="sxs-lookup"><span data-stu-id="de7dd-194">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="de7dd-195">其他 `NavLink` 组件特性会传递到呈现的定位点标记。</span><span class="sxs-lookup"><span data-stu-id="de7dd-195">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="de7dd-196">在下面的示例中，`NavLink` 组件包含 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="de7dd-196">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="de7dd-197">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="de7dd-197">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="de7dd-198">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="de7dd-198">URI and navigation state helpers</span></span>

<span data-ttu-id="de7dd-199">使用 `Microsoft.AspNetCore.Components.NavigationManager` 在代码中C#处理 uri 和导航。</span><span class="sxs-lookup"><span data-stu-id="de7dd-199">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="de7dd-200">`NavigationManager` 提供下表中显示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="de7dd-200">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="de7dd-201">成员</span><span class="sxs-lookup"><span data-stu-id="de7dd-201">Member</span></span> | <span data-ttu-id="de7dd-202">描述</span><span class="sxs-lookup"><span data-stu-id="de7dd-202">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="de7dd-203">获取当前的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="de7dd-203">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="de7dd-204">获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI （带有尾随斜杠）。</span><span class="sxs-lookup"><span data-stu-id="de7dd-204">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="de7dd-205">通常，`BaseUri` 对应于*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）中文档的 `<base>` 元素上的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="de7dd-205">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="de7dd-206">定位到指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="de7dd-206">Navigates to the specified URI.</span></span> <span data-ttu-id="de7dd-207">如果 `true` `forceLoad`：</span><span class="sxs-lookup"><span data-stu-id="de7dd-207">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="de7dd-208">客户端路由被绕过。</span><span class="sxs-lookup"><span data-stu-id="de7dd-208">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="de7dd-209">无论 URI 是否通常由客户端路由器处理，浏览器都被迫从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="de7dd-209">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="de7dd-210">导航位置发生更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="de7dd-210">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="de7dd-211">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="de7dd-211">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="de7dd-212">给定基 URI （例如，以前由 `GetBaseUri` 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="de7dd-212">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="de7dd-213">选择该按钮时，以下组件将导航到应用的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="de7dd-213">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
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
