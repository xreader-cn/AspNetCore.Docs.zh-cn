---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求，以及如何在 NavLink 组件中路由请求。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: d348908261c51b477aa698a407266d05c0df5a33
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800340"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="2ab5f-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="2ab5f-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="2ab5f-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ab5f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2ab5f-105">了解如何路由请求，以及如何使用该`NavLink`组件在 Blazor apps 中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="2ab5f-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="2ab5f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="2ab5f-107">Blazor 服务器端集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="2ab5f-108">ASP.NET Core 的应用程序配置为使用`MapBlazorHub`中`Startup.Configure`的交互式组件接受传入连接：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="2ab5f-109">路由模板</span><span class="sxs-lookup"><span data-stu-id="2ab5f-109">Route templates</span></span>

<span data-ttu-id="2ab5f-110">`Router`组件允许路由到具有指定路由的每个组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-110">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="2ab5f-111">该`Router`组件将出现在*app.config*文件中：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-111">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="2ab5f-112">编译包含 `@page`指令的 razor 文件时，将为生成的<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>类提供指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-112">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="2ab5f-113">在运行时， `RouteView`组件：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-113">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="2ab5f-114">接收来自的`Router` ，以及任何所需的参数。 `RouteData`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-114">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="2ab5f-115">使用指定的参数呈现指定组件及其布局（或可选默认布局）。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-115">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="2ab5f-116">您可以选择使用布局`DefaultLayout`类指定参数，以便为未指定布局的组件使用。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-116">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="2ab5f-117">默认的 Blazor 模板指定`MainLayout`组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-117">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="2ab5f-118">*MainLayout*位于模板项目的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-118">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="2ab5f-119">有关布局的详细信息，请<xref:blazor/layouts>参阅。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-119">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="2ab5f-120">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-120">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="2ab5f-121">以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-121">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="2ab5f-122">若要正确解析 url，应用必须在其*wwwroot/index.html*文件（Blazor 客户端）或*Pages/_Host*文件（Blazor 服务器端）中包含一个`href` `<base>`标记，并在属性中指定应用程序基路径（`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="2ab5f-122">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="2ab5f-123">有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-123">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="2ab5f-124">当找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="2ab5f-124">Provide custom content when content isn't found</span></span>

<span data-ttu-id="2ab5f-125">如果找不到请求的路由的内容，该组件允许应用指定自定义内容。`Router`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-125">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="2ab5f-126">在*app.config*文件中，在`NotFound` `Router`组件的 template 参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-126">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="2ab5f-127">`<NotFound>`标记的内容可以包括任意项，如其他交互组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-127">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="2ab5f-128">若要将默认布局`NotFound`应用于内容，请参阅。 <xref:blazor/layouts></span><span class="sxs-lookup"><span data-stu-id="2ab5f-128">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="2ab5f-129">从多个程序集中路由到组件</span><span class="sxs-lookup"><span data-stu-id="2ab5f-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="2ab5f-130">使用参数指定在搜索可路由组件时`Router`要考虑的组件的其他程序集。 `AdditionalAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-130">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="2ab5f-131">除了指定的程序集`AppAssembly`外，还会考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-131">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="2ab5f-132">在下面的示例中`Component1` ，是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-132">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="2ab5f-133">下面`AdditionalAssemblies`的示例将导致路由`Component1`支持：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-133">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="2ab5f-134">< 路由器 AppAssembly = "typeof （程序）。程序集 "AdditionalAssemblies =" new [] {typeof （Component1）。Assembly} > .。。</Router></span><span class="sxs-lookup"><span data-stu-id="2ab5f-134"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="2ab5f-135">路由参数</span><span class="sxs-lookup"><span data-stu-id="2ab5f-135">Route parameters</span></span>

<span data-ttu-id="2ab5f-136">路由器使用路由参数来填充具有相同名称（不区分大小写）的相应组件参数：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-136">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="2ab5f-137">ASP.NET Core 3.0 预览中的 Blazor 应用不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-137">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="2ab5f-138">前面`@page`的示例中应用了两个指令。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-138">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="2ab5f-139">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-139">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2ab5f-140">第二`@page`个指令`{text}`采用路由参数，并将值`Text`分配给属性。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-140">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="2ab5f-141">路由约束</span><span class="sxs-lookup"><span data-stu-id="2ab5f-141">Route constraints</span></span>

<span data-ttu-id="2ab5f-142">路由约束向组件强制执行对路由段的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-142">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="2ab5f-143">在下面的示例中，指向`Users`组件的路由仅在以下情况下才匹配：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-143">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="2ab5f-144">请求 URL 上存在路由段。`Id`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-144">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="2ab5f-145">段是整数（`int`）。 `Id`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-145">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="2ab5f-146">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-146">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="2ab5f-147">有关与固定区域性匹配的路由约束，请参阅表下面的警告以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-147">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="2ab5f-148">约束</span><span class="sxs-lookup"><span data-stu-id="2ab5f-148">Constraint</span></span> | <span data-ttu-id="2ab5f-149">示例</span><span class="sxs-lookup"><span data-stu-id="2ab5f-149">Example</span></span>           | <span data-ttu-id="2ab5f-150">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="2ab5f-150">Example Matches</span></span>                                                                  | <span data-ttu-id="2ab5f-151">固定条件</span><span class="sxs-lookup"><span data-stu-id="2ab5f-151">Invariant</span></span><br><span data-ttu-id="2ab5f-152">区域性</span><span class="sxs-lookup"><span data-stu-id="2ab5f-152">culture</span></span><br><span data-ttu-id="2ab5f-153">匹配</span><span class="sxs-lookup"><span data-stu-id="2ab5f-153">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="2ab5f-154">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-154">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="2ab5f-155">No</span><span class="sxs-lookup"><span data-stu-id="2ab5f-155">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="2ab5f-156">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-156">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="2ab5f-157">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-157">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="2ab5f-158">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-158">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="2ab5f-159">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-159">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="2ab5f-160">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-160">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2ab5f-161">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-161">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="2ab5f-162">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-162">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2ab5f-163">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-163">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="2ab5f-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2ab5f-165">No</span><span class="sxs-lookup"><span data-stu-id="2ab5f-165">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="2ab5f-166">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-166">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2ab5f-167">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-167">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="2ab5f-168">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-168">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2ab5f-169">是</span><span class="sxs-lookup"><span data-stu-id="2ab5f-169">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="2ab5f-170">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-170">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2ab5f-171">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-171">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="2ab5f-172">带有包含点的 Url 的路由</span><span class="sxs-lookup"><span data-stu-id="2ab5f-172">Routing with URLs that contain dots</span></span>

<span data-ttu-id="2ab5f-173">在 Blazor 服务器端应用中， *_Host*中的默认路由是`/` （`@page "/"`）。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-173">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="2ab5f-174">由于 url 显示为请求文件，因此`.`包含点（）的请求 URL 不能与默认路由匹配。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-174">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="2ab5f-175">对于不存在的静态文件，Blazor 应用返回 " *404-未找到*" 响应。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-175">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="2ab5f-176">若要使用包含点的路由，请使用以下路由模板配置 *_Host* ：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-176">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="2ab5f-177">该`"/{**path}"`模板包括：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-177">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="2ab5f-178">双星号*catch-all*语法（`**`）捕获跨多个文件夹边界的路径，而无需编码正`/`斜杠（）。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-178">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="2ab5f-179">`path`路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-179">A `path` route parameter name.</span></span>

<span data-ttu-id="2ab5f-180">有关详细信息，请参阅 <xref:fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-180">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="2ab5f-181">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="2ab5f-181">NavLink component</span></span>

<span data-ttu-id="2ab5f-182">创建导航链接时，请使用`<a>`组件来代替HTMLhyperlink元素（）。`NavLink`</span><span class="sxs-lookup"><span data-stu-id="2ab5f-182">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="2ab5f-183">组件`NavLink`的行为`<a>`类似于元素`active` ，只不过它会根据 CSS 类是否`href`与当前 URL 匹配来进行切换。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-183">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="2ab5f-184">`active`类可帮助用户了解在显示的导航链接中哪一页是活动页。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-184">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="2ab5f-185">以下`NavMenu`组件创建演示如何使用`NavLink`组件的[启动](https://getbootstrap.com/docs/)导航栏：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-185">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="2ab5f-186">可以将两`NavLinkMatch`个选项分配`Match`给该`<NavLink>`元素的属性：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-186">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="2ab5f-187">`NavLinkMatch.All`当与整个当前 URL 匹配时，处于活动状态。`NavLink` &ndash;</span><span class="sxs-lookup"><span data-stu-id="2ab5f-187">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="2ab5f-188">`NavLinkMatch.Prefix`（*默认值*）&ndash; 当与当前URL的任何前缀匹配时，`NavLink`将处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-188">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="2ab5f-189">在前面的示例中，home `NavLink` `href=""`与 home `active` URL 匹配，仅在应用的默认基路径 URL （例如， `https://localhost:5001/`）中接收 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-189">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="2ab5f-190">当用户`NavLink`使用`MyComponent`前缀`active` （例如`https://localhost:5001/MyComponent` 和`https://localhost:5001/MyComponent/AnotherSegment`）访问任何 URL 时，第二个会接收该类。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-190">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="2ab5f-191">其他`NavLink`组件特性会传递到呈现的定位点标记。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-191">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="2ab5f-192">在下面的示例中， `NavLink`组件`target`包含特性：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-192">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="2ab5f-193">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-193">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="2ab5f-194">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="2ab5f-194">URI and navigation state helpers</span></span>

<span data-ttu-id="2ab5f-195">使用`Microsoft.AspNetCore.Components.NavigationManager`在代码中C#处理 uri 和导航。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-195">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="2ab5f-196">`NavigationManager`提供下表中显示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-196">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="2ab5f-197">成员</span><span class="sxs-lookup"><span data-stu-id="2ab5f-197">Member</span></span> | <span data-ttu-id="2ab5f-198">描述</span><span class="sxs-lookup"><span data-stu-id="2ab5f-198">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="2ab5f-199">获取当前的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-199">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="2ab5f-200">获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI （带有尾随斜杠）。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-200">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="2ab5f-201">通常， `BaseUri`与*wwwroot/index.html* （ `href` Blazor 客户端）或`<base>` *Pages/_Host* （Blazor 服务器端）中文档的元素的属性相对应。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-201">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="2ab5f-202">定位到指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-202">Navigates to the specified URI.</span></span> <span data-ttu-id="2ab5f-203">如果`forceLoad`为`true`：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-203">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="2ab5f-204">客户端路由被绕过。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-204">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="2ab5f-205">无论 URI 是否通常由客户端路由器处理，浏览器都被迫从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-205">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="2ab5f-206">导航位置发生更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-206">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="2ab5f-207">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-207">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="2ab5f-208">给定基 uri （例如，先前由返回的`GetBaseUri`uri），会将绝对 uri 转换为相对于基 uri 前缀的 uri。</span><span class="sxs-lookup"><span data-stu-id="2ab5f-208">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="2ab5f-209">选择该按钮时，以下组件将`Counter`导航到应用的组件：</span><span class="sxs-lookup"><span data-stu-id="2ab5f-209">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
