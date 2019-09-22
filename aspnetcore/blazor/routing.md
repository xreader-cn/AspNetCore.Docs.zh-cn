---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求，以及如何在 NavLink 组件中路由请求。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2019
uid: blazor/routing
ms.openlocfilehash: d6fb3f03be94ff99ac3ed434265e6cd6b752c625
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176399"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="2ca54-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="2ca54-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="2ca54-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ca54-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2ca54-105">了解如何路由请求，以及如何使用该`NavLink`组件在 Blazor apps 中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="2ca54-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="2ca54-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="2ca54-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="2ca54-107">Blazor 服务器已集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="2ca54-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="2ca54-108">ASP.NET Core 的应用程序配置为使用`MapBlazorHub`中`Startup.Configure`的交互式组件接受传入连接：</span><span class="sxs-lookup"><span data-stu-id="2ca54-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="2ca54-109">最典型的配置是将所有请求路由到 Razor 页，该页面充当 Blazor 服务器应用的服务器端部分的主机。</span><span class="sxs-lookup"><span data-stu-id="2ca54-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="2ca54-110">按照约定，*主机*页通常名为 *_Host*。</span><span class="sxs-lookup"><span data-stu-id="2ca54-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="2ca54-111">主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以低优先级操作。</span><span class="sxs-lookup"><span data-stu-id="2ca54-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="2ca54-112">当其他路由不匹配时，将考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="2ca54-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="2ca54-113">这允许应用使用其他控制器和页面，而不会干扰 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="2ca54-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="2ca54-114">路由模板</span><span class="sxs-lookup"><span data-stu-id="2ca54-114">Route templates</span></span>

<span data-ttu-id="2ca54-115">`Router`组件允许路由到具有指定路由的每个组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="2ca54-116">该`Router`组件将出现在*app.config*文件中：</span><span class="sxs-lookup"><span data-stu-id="2ca54-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="2ca54-117">编译包含 `@page`指令的 razor 文件时，将为生成的<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>类提供指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="2ca54-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="2ca54-118">在运行时， `RouteView`组件：</span><span class="sxs-lookup"><span data-stu-id="2ca54-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="2ca54-119">接收来自的`Router` ，以及任何所需的参数。 `RouteData`</span><span class="sxs-lookup"><span data-stu-id="2ca54-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="2ca54-120">使用指定的参数呈现指定组件及其布局（或可选默认布局）。</span><span class="sxs-lookup"><span data-stu-id="2ca54-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="2ca54-121">您可以选择使用布局`DefaultLayout`类指定参数，以便为未指定布局的组件使用。</span><span class="sxs-lookup"><span data-stu-id="2ca54-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="2ca54-122">默认的 Blazor 模板指定`MainLayout`组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="2ca54-123">*MainLayout*位于模板项目的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2ca54-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="2ca54-124">有关布局的详细信息，请<xref:blazor/layouts>参阅。</span><span class="sxs-lookup"><span data-stu-id="2ca54-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="2ca54-125">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="2ca54-126">以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求：</span><span class="sxs-lookup"><span data-stu-id="2ca54-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="2ca54-127">若要正确解析 url，应用必须在其*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/_Host*文件（Blazor 服务器）中包含一个`href` `<base>`标记，该标记具有属性中指定的应用程序基路径（`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="2ca54-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="2ca54-128">有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="2ca54-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="2ca54-129">当找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="2ca54-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="2ca54-130">如果找不到请求的路由的内容，该组件允许应用指定自定义内容。`Router`</span><span class="sxs-lookup"><span data-stu-id="2ca54-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="2ca54-131">在*app.config*文件中，在`NotFound` `Router`组件的 template 参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="2ca54-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="2ca54-132">`<NotFound>`标记的内容可以包括任意项，如其他交互组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="2ca54-133">若要将默认布局`NotFound`应用于内容，请参阅。 <xref:blazor/layouts></span><span class="sxs-lookup"><span data-stu-id="2ca54-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="2ca54-134">从多个程序集中路由到组件</span><span class="sxs-lookup"><span data-stu-id="2ca54-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="2ca54-135">使用参数指定在搜索可路由组件时`Router`要考虑的组件的其他程序集。 `AdditionalAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2ca54-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="2ca54-136">除了指定的程序集`AppAssembly`外，还会考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="2ca54-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="2ca54-137">在下面的示例中`Component1` ，是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="2ca54-138">下面`AdditionalAssemblies`的示例将导致路由`Component1`支持：</span><span class="sxs-lookup"><span data-stu-id="2ca54-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="2ca54-139">< 路由器 AppAssembly = "typeof （程序）。程序集 "AdditionalAssemblies =" new [] {typeof （Component1）。Assembly} > .。。</Router></span><span class="sxs-lookup"><span data-stu-id="2ca54-139"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="2ca54-140">路由参数</span><span class="sxs-lookup"><span data-stu-id="2ca54-140">Route parameters</span></span>

<span data-ttu-id="2ca54-141">路由器使用路由参数来填充具有相同名称（不区分大小写）的相应组件参数：</span><span class="sxs-lookup"><span data-stu-id="2ca54-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="2ca54-142">ASP.NET Core 3.0 预览中的 Blazor 应用不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="2ca54-142">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="2ca54-143">前面`@page`的示例中应用了两个指令。</span><span class="sxs-lookup"><span data-stu-id="2ca54-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="2ca54-144">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2ca54-145">第二`@page`个指令`{text}`采用路由参数，并将值`Text`分配给属性。</span><span class="sxs-lookup"><span data-stu-id="2ca54-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="2ca54-146">路由约束</span><span class="sxs-lookup"><span data-stu-id="2ca54-146">Route constraints</span></span>

<span data-ttu-id="2ca54-147">路由约束向组件强制执行对路由段的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="2ca54-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="2ca54-148">在下面的示例中，指向`Users`组件的路由仅在以下情况下才匹配：</span><span class="sxs-lookup"><span data-stu-id="2ca54-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="2ca54-149">请求 URL 上存在路由段。`Id`</span><span class="sxs-lookup"><span data-stu-id="2ca54-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="2ca54-150">段是整数（`int`）。 `Id`</span><span class="sxs-lookup"><span data-stu-id="2ca54-150">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="2ca54-151">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="2ca54-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="2ca54-152">有关与固定区域性匹配的路由约束，请参阅表下面的警告以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="2ca54-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="2ca54-153">约束</span><span class="sxs-lookup"><span data-stu-id="2ca54-153">Constraint</span></span> | <span data-ttu-id="2ca54-154">示例</span><span class="sxs-lookup"><span data-stu-id="2ca54-154">Example</span></span>           | <span data-ttu-id="2ca54-155">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="2ca54-155">Example Matches</span></span>                                                                  | <span data-ttu-id="2ca54-156">固定条件</span><span class="sxs-lookup"><span data-stu-id="2ca54-156">Invariant</span></span><br><span data-ttu-id="2ca54-157">区域性</span><span class="sxs-lookup"><span data-stu-id="2ca54-157">culture</span></span><br><span data-ttu-id="2ca54-158">匹配</span><span class="sxs-lookup"><span data-stu-id="2ca54-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="2ca54-159">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="2ca54-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="2ca54-160">No</span><span class="sxs-lookup"><span data-stu-id="2ca54-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="2ca54-161">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2ca54-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="2ca54-162">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="2ca54-163">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2ca54-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="2ca54-164">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="2ca54-165">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2ca54-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2ca54-166">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="2ca54-167">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2ca54-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2ca54-168">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="2ca54-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2ca54-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2ca54-170">No</span><span class="sxs-lookup"><span data-stu-id="2ca54-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="2ca54-171">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2ca54-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2ca54-172">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="2ca54-173">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2ca54-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2ca54-174">是</span><span class="sxs-lookup"><span data-stu-id="2ca54-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="2ca54-175">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="2ca54-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2ca54-176">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="2ca54-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="2ca54-177">带有包含点的 Url 的路由</span><span class="sxs-lookup"><span data-stu-id="2ca54-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="2ca54-178">在 Blazor 服务器应用程序中， *_Host*中的默认路由是`/` （`@page "/"`）。</span><span class="sxs-lookup"><span data-stu-id="2ca54-178">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="2ca54-179">由于 url 显示为请求文件，因此`.`包含点（）的请求 URL 不能与默认路由匹配。</span><span class="sxs-lookup"><span data-stu-id="2ca54-179">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="2ca54-180">对于不存在的静态文件，Blazor 应用返回 " *404-未找到*" 响应。</span><span class="sxs-lookup"><span data-stu-id="2ca54-180">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="2ca54-181">若要使用包含点的路由，请使用以下路由模板配置 *_Host* ：</span><span class="sxs-lookup"><span data-stu-id="2ca54-181">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="2ca54-182">该`"/{**path}"`模板包括：</span><span class="sxs-lookup"><span data-stu-id="2ca54-182">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="2ca54-183">双星号*catch-all*语法（`**`）捕获跨多个文件夹边界的路径，而无需编码正`/`斜杠（）。</span><span class="sxs-lookup"><span data-stu-id="2ca54-183">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="2ca54-184">`path`路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="2ca54-184">A `path` route parameter name.</span></span>

<span data-ttu-id="2ca54-185">有关详细信息，请参阅 <xref:fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="2ca54-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="2ca54-186">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="2ca54-186">NavLink component</span></span>

<span data-ttu-id="2ca54-187">创建导航链接时，请使用`<a>`组件来代替HTMLhyperlink元素（）。`NavLink`</span><span class="sxs-lookup"><span data-stu-id="2ca54-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="2ca54-188">组件`NavLink`的行为`<a>`类似于元素`active` ，只不过它会根据 CSS 类是否`href`与当前 URL 匹配来进行切换。</span><span class="sxs-lookup"><span data-stu-id="2ca54-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="2ca54-189">`active`类可帮助用户了解在显示的导航链接中哪一页是活动页。</span><span class="sxs-lookup"><span data-stu-id="2ca54-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="2ca54-190">以下`NavMenu`组件创建演示如何使用`NavLink`组件的[启动](https://getbootstrap.com/docs/)导航栏：</span><span class="sxs-lookup"><span data-stu-id="2ca54-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="2ca54-191">可以将两`NavLinkMatch`个选项分配`Match`给该`<NavLink>`元素的属性：</span><span class="sxs-lookup"><span data-stu-id="2ca54-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="2ca54-192">`NavLinkMatch.All`当与整个当前 URL 匹配时，处于活动状态。`NavLink` &ndash;</span><span class="sxs-lookup"><span data-stu-id="2ca54-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="2ca54-193">`NavLinkMatch.Prefix`（*默认值*）&ndash; 当与当前URL的任何前缀匹配时，`NavLink`将处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="2ca54-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="2ca54-194">在前面的示例中，home `NavLink` `href=""`与 home `active` URL 匹配，仅在应用的默认基路径 URL （例如， `https://localhost:5001/`）中接收 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="2ca54-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="2ca54-195">当用户`NavLink`使用`MyComponent`前缀`active` （例如`https://localhost:5001/MyComponent` 和`https://localhost:5001/MyComponent/AnotherSegment`）访问任何 URL 时，第二个会接收该类。</span><span class="sxs-lookup"><span data-stu-id="2ca54-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="2ca54-196">其他`NavLink`组件特性会传递到呈现的定位点标记。</span><span class="sxs-lookup"><span data-stu-id="2ca54-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="2ca54-197">在下面的示例中， `NavLink`组件`target`包含特性：</span><span class="sxs-lookup"><span data-stu-id="2ca54-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="2ca54-198">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="2ca54-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="2ca54-199">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="2ca54-199">URI and navigation state helpers</span></span>

<span data-ttu-id="2ca54-200">使用`Microsoft.AspNetCore.Components.NavigationManager`在代码中C#处理 uri 和导航。</span><span class="sxs-lookup"><span data-stu-id="2ca54-200">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="2ca54-201">`NavigationManager`提供下表中显示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="2ca54-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="2ca54-202">成员</span><span class="sxs-lookup"><span data-stu-id="2ca54-202">Member</span></span> | <span data-ttu-id="2ca54-203">描述</span><span class="sxs-lookup"><span data-stu-id="2ca54-203">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="2ca54-204">获取当前的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2ca54-204">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="2ca54-205">获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI （带有尾随斜杠）。</span><span class="sxs-lookup"><span data-stu-id="2ca54-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="2ca54-206">通常， `BaseUri`对应于*wwwroot/index.html* （Blazor WebAssembly）或`<base>` *Pages/_Host* （Blazor Server）中文档的元素的`href`属性。</span><span class="sxs-lookup"><span data-stu-id="2ca54-206">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="2ca54-207">定位到指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="2ca54-207">Navigates to the specified URI.</span></span> <span data-ttu-id="2ca54-208">如果`forceLoad`为`true`：</span><span class="sxs-lookup"><span data-stu-id="2ca54-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="2ca54-209">客户端路由被绕过。</span><span class="sxs-lookup"><span data-stu-id="2ca54-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="2ca54-210">无论 URI 是否通常由客户端路由器处理，浏览器都被迫从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="2ca54-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="2ca54-211">导航位置发生更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="2ca54-211">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="2ca54-212">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2ca54-212">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="2ca54-213">给定基 uri （例如，先前由返回的`GetBaseUri`uri），会将绝对 uri 转换为相对于基 uri 前缀的 uri。</span><span class="sxs-lookup"><span data-stu-id="2ca54-213">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="2ca54-214">选择该按钮时，以下组件将`Counter`导航到应用的组件：</span><span class="sxs-lookup"><span data-stu-id="2ca54-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
