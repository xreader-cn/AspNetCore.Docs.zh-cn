---
title: 路由的 razor 组件
author: guardrex
description: 了解如何将请求路由在应用和有关 NavLink 组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/routing
ms.openlocfilehash: ef82fa7e0d571979a43fd8ce712bf4f22c06f9a7
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515428"
---
# <a name="razor-components-routing"></a><span data-ttu-id="38e5b-103">路由的 razor 组件</span><span class="sxs-lookup"><span data-stu-id="38e5b-103">Razor Components routing</span></span>

<span data-ttu-id="38e5b-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="38e5b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="38e5b-105">了解如何将请求路由在应用和有关 NavLink 组件。</span><span class="sxs-lookup"><span data-stu-id="38e5b-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="38e5b-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="38e5b-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="38e5b-107">Razor 组件集成到[ASP.NET Core 路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="38e5b-107">Razor Components are integrated into [ASP.NET Core routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="38e5b-108">ASP.NET Core 应用配置为接受传入连接的具有交互式 Razor 组件`MapComponentHub<TComponent>`在`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="38e5b-108">An ASP.NET Core app is configured to accept incoming connections for interactive Razor Components with `MapComponentHub<TComponent>` in `Startup.Configure`.</span></span> `MapComponentHub` <span data-ttu-id="38e5b-109">指定的根组件`App`应匹配选择器的 DOM 元素中呈现`app`:</span><span class="sxs-lookup"><span data-stu-id="38e5b-109">specifies that the root component `App` should be rendered within a DOM element matching the selector `app`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a><span data-ttu-id="38e5b-110">路由模板</span><span class="sxs-lookup"><span data-stu-id="38e5b-110">Route templates</span></span>

<span data-ttu-id="38e5b-111">`<Router>`组件允许路由和路由模板提供给每个可访问的组件。</span><span class="sxs-lookup"><span data-stu-id="38e5b-111">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="38e5b-112">`<Router>`部分显示在*Components/App.razor*文件：</span><span class="sxs-lookup"><span data-stu-id="38e5b-112">The `<Router>` component appears in the *Components/App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="38e5b-113">当 *.razor*或 *.cshtml*文件`@page`编译指令，则生成的类提供<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>指定路由模板。</span><span class="sxs-lookup"><span data-stu-id="38e5b-113">When a *.razor* or *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="38e5b-114">在运行时，路由器将查看与组件类`RouteAttribute`并呈现任何组件有匹配的请求的 URL 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="38e5b-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="38e5b-115">多个路由模板可以应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="38e5b-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="38e5b-116">以下组件响应的请求`/BlazorRoute`和`/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="38e5b-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` <span data-ttu-id="38e5b-117">支持设置呈现时请求的路由的回退组件进行解析。</span><span class="sxs-lookup"><span data-stu-id="38e5b-117">supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="38e5b-118">通过设置启用此选择加入方案`FallbackComponent`回退组件类的类型参数。</span><span class="sxs-lookup"><span data-stu-id="38e5b-118">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="38e5b-119">下面的示例设置中定义的组件*Pages/MyFallbackRazorComponent.cshtml*是回退组件`<Router>`:</span><span class="sxs-lookup"><span data-stu-id="38e5b-119">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="38e5b-120">若要正确生成的路由，该应用程序必须包括`<base>`标记中的其*wwwroot/index.html*文件中指定的应用程序基路径`href`属性 (`<base href="/">`)。</span><span class="sxs-lookup"><span data-stu-id="38e5b-120">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="38e5b-121">有关详细信息，请参阅 <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="38e5b-121">For more information, see <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="38e5b-122">路由参数</span><span class="sxs-lookup"><span data-stu-id="38e5b-122">Route parameters</span></span>

<span data-ttu-id="38e5b-123">路由器使用路由参数来填充相应的组件参数具有相同名称 （不区分大小写）：</span><span class="sxs-lookup"><span data-stu-id="38e5b-123">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="38e5b-124">可选参数不受支持，因此两个`@page`指令收取在上面的示例。</span><span class="sxs-lookup"><span data-stu-id="38e5b-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="38e5b-125">第一个可以导航到不带参数的组件。</span><span class="sxs-lookup"><span data-stu-id="38e5b-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="38e5b-126">第二个`@page`指令采用`{text}`路由参数和值赋给`Text`属性。</span><span class="sxs-lookup"><span data-stu-id="38e5b-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="38e5b-127">路由约束</span><span class="sxs-lookup"><span data-stu-id="38e5b-127">Route constraints</span></span>

<span data-ttu-id="38e5b-128">路由约束强制实施类型到一个组件路由段进行匹配。</span><span class="sxs-lookup"><span data-stu-id="38e5b-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="38e5b-129">在以下示例中，如果只与匹配的路由到用户组件：</span><span class="sxs-lookup"><span data-stu-id="38e5b-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="38e5b-130">`Id`路由段是存在于请求 URL 上。</span><span class="sxs-lookup"><span data-stu-id="38e5b-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="38e5b-131">`Id`段是一个整数 (`int`)。</span><span class="sxs-lookup"><span data-stu-id="38e5b-131">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

<span data-ttu-id="38e5b-132">提供下表中所示的路由约束。</span><span class="sxs-lookup"><span data-stu-id="38e5b-132">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="38e5b-133">使用固定区域性匹配的路由约束，请参阅下表了解详细信息的警告。</span><span class="sxs-lookup"><span data-stu-id="38e5b-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="38e5b-134">约束</span><span class="sxs-lookup"><span data-stu-id="38e5b-134">Constraint</span></span> | <span data-ttu-id="38e5b-135">示例</span><span class="sxs-lookup"><span data-stu-id="38e5b-135">Example</span></span>           | <span data-ttu-id="38e5b-136">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="38e5b-136">Example Matches</span></span>                                                                  | <span data-ttu-id="38e5b-137">固定条件</span><span class="sxs-lookup"><span data-stu-id="38e5b-137">Invariant</span></span><br><span data-ttu-id="38e5b-138">区域性</span><span class="sxs-lookup"><span data-stu-id="38e5b-138">culture</span></span><br><span data-ttu-id="38e5b-139">匹配</span><span class="sxs-lookup"><span data-stu-id="38e5b-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`<span data-ttu-id="38e5b-140">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-140">,</span></span> `FALSE`                                                                  | <span data-ttu-id="38e5b-141">否</span><span class="sxs-lookup"><span data-stu-id="38e5b-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`<span data-ttu-id="38e5b-142">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-142">,</span></span> `2016-12-31 7:32pm`                                                | <span data-ttu-id="38e5b-143">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | `49.99`<span data-ttu-id="38e5b-144">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-144">,</span></span> `-1,000.01`                                                             | <span data-ttu-id="38e5b-145">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | `1.234`<span data-ttu-id="38e5b-146">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-146">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="38e5b-147">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | `1.234`<span data-ttu-id="38e5b-148">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-148">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="38e5b-149">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`<span data-ttu-id="38e5b-150">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-150">,</span></span> `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | <span data-ttu-id="38e5b-151">否</span><span class="sxs-lookup"><span data-stu-id="38e5b-151">No</span></span>                               |
| `int`      | `{id:int}`        | `123456789`<span data-ttu-id="38e5b-152">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-152">,</span></span> `-123456789`                                                        | <span data-ttu-id="38e5b-153">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | `123456789`<span data-ttu-id="38e5b-154">,</span><span class="sxs-lookup"><span data-stu-id="38e5b-154">,</span></span> `-123456789`                                                        | <span data-ttu-id="38e5b-155">是</span><span class="sxs-lookup"><span data-stu-id="38e5b-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="38e5b-156">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="38e5b-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="38e5b-157">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="38e5b-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="38e5b-158">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="38e5b-158">NavLink component</span></span>

<span data-ttu-id="38e5b-159">使用取代 HTML NavLink 组件`<a>`元素创建导航链接时。</span><span class="sxs-lookup"><span data-stu-id="38e5b-159">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="38e5b-160">NavLink 组件的行为类似于`<a>`元素，但它切换`active`CSS 类是否基于其`href`当前 URL 匹配。</span><span class="sxs-lookup"><span data-stu-id="38e5b-160">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="38e5b-161">`active`类可帮助用户了解哪页不显示导航链接中的活动页面。</span><span class="sxs-lookup"><span data-stu-id="38e5b-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="38e5b-162">以下 NavMenu 组件创建[Bootstrap](https://getbootstrap.com/docs/)演示了如何使用 NavLink 组件的导航栏：</span><span class="sxs-lookup"><span data-stu-id="38e5b-162">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="38e5b-163">有两个`NavLinkMatch`选项：</span><span class="sxs-lookup"><span data-stu-id="38e5b-163">There are two `NavLinkMatch` options:</span></span>

* `NavLinkMatch.All` <span data-ttu-id="38e5b-164">&ndash; 指定，它与匹配整个当前 URL 时，NavLink 应处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="38e5b-164">&ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* `NavLinkMatch.Prefix` <span data-ttu-id="38e5b-165">&ndash; 指定，它与匹配的任何前缀的当前 URL 时，NavLink 应处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="38e5b-165">&ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="38e5b-166">在前面的示例中，主页 NavLink (`href=""`) 匹配的所有 Url 并始终接收`active`CSS 类。</span><span class="sxs-lookup"><span data-stu-id="38e5b-166">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="38e5b-167">仅接收第二个 NavLink`active`类在用户访问 Blazor 路由组件 (`href="BlazorRoute"`)。</span><span class="sxs-lookup"><span data-stu-id="38e5b-167">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>
