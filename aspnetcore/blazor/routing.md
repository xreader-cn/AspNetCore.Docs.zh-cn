---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求, 以及如何在 NavLink 组件中路由请求。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/routing
ms.openlocfilehash: 197b1a91b3540d21639c3ee775b2c490da7b23fe
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030399"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="6fb84-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="6fb84-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="6fb84-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6fb84-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6fb84-105">了解如何路由请求, 以及如何使用该`NavLink`组件在 Blazor apps 中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="6fb84-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="6fb84-106">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="6fb84-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="6fb84-107">Blazor 服务器端集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="6fb84-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6fb84-108">ASP.NET Core 的应用程序配置为使用`MapBlazorHub`中`Startup.Configure`的交互式组件接受传入连接:</span><span class="sxs-lookup"><span data-stu-id="6fb84-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="6fb84-109">路由模板</span><span class="sxs-lookup"><span data-stu-id="6fb84-109">Route templates</span></span>

<span data-ttu-id="6fb84-110">`Router`组件启用路由, 并向每个可访问的组件提供路由模板。</span><span class="sxs-lookup"><span data-stu-id="6fb84-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="6fb84-111">该`Router`组件将出现在*app.config*文件中:</span><span class="sxs-lookup"><span data-stu-id="6fb84-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="6fb84-112">在 Blazor 服务器端应用中:</span><span class="sxs-lookup"><span data-stu-id="6fb84-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="6fb84-113">在 Blazor 客户端应用中:</span><span class="sxs-lookup"><span data-stu-id="6fb84-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="6fb84-114">编译包含 `@page`指令的 razor 文件时, 将为生成的<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>类提供指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="6fb84-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="6fb84-115">在运行时, 路由器使用`RouteAttribute`查找组件类, 并使用与请求的 URL 匹配的路由模板来呈现组件。</span><span class="sxs-lookup"><span data-stu-id="6fb84-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="6fb84-116">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="6fb84-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6fb84-117">以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求:</span><span class="sxs-lookup"><span data-stu-id="6fb84-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="6fb84-118">若要正确生成路由, 应用必须在其`<base>` *wwwroot/index.html*文件 (Blazor 客户端) 或*Pages/_Host*文件 (Blazor 服务器端) 中包含一个标记, 并在`href`属性中指定应用程序基路径 (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="6fb84-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="6fb84-119">有关详细信息，请参阅 <xref:host-and-deploy/blazor/client-side#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="6fb84-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="6fb84-120">当找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="6fb84-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="6fb84-121">如果找不到请求的路由的内容, 该组件允许应用指定自定义内容。`Router`</span><span class="sxs-lookup"><span data-stu-id="6fb84-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="6fb84-122">在*app.config*文件中, 在`<NotFoundContent>` `Router`组件的元素中设置自定义内容:</span><span class="sxs-lookup"><span data-stu-id="6fb84-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="6fb84-123">的内容`<NotFoundContent>`可以包括任意项, 如其他交互组件。</span><span class="sxs-lookup"><span data-stu-id="6fb84-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="6fb84-124">路由参数</span><span class="sxs-lookup"><span data-stu-id="6fb84-124">Route parameters</span></span>

<span data-ttu-id="6fb84-125">路由器使用路由参数来填充具有相同名称 (不区分大小写) 的相应组件参数:</span><span class="sxs-lookup"><span data-stu-id="6fb84-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="6fb84-126">ASP.NET Core 3.0 预览中的 Blazor 应用不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="6fb84-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="6fb84-127">前面`@page`的示例中应用了两个指令。</span><span class="sxs-lookup"><span data-stu-id="6fb84-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="6fb84-128">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="6fb84-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6fb84-129">第二`@page`个指令`{text}`采用路由参数, 并将值`Text`分配给属性。</span><span class="sxs-lookup"><span data-stu-id="6fb84-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="6fb84-130">路由约束</span><span class="sxs-lookup"><span data-stu-id="6fb84-130">Route constraints</span></span>

<span data-ttu-id="6fb84-131">路由约束向组件强制执行对路由段的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="6fb84-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="6fb84-132">在下面的示例中, 指向`Users`组件的路由仅在以下情况下才匹配:</span><span class="sxs-lookup"><span data-stu-id="6fb84-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="6fb84-133">请求 URL 上存在路由段。`Id`</span><span class="sxs-lookup"><span data-stu-id="6fb84-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="6fb84-134">段是整数 (`int`)。 `Id`</span><span class="sxs-lookup"><span data-stu-id="6fb84-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="6fb84-135">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="6fb84-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="6fb84-136">有关与固定区域性匹配的路由约束, 请参阅表下面的警告以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="6fb84-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="6fb84-137">约束</span><span class="sxs-lookup"><span data-stu-id="6fb84-137">Constraint</span></span> | <span data-ttu-id="6fb84-138">示例</span><span class="sxs-lookup"><span data-stu-id="6fb84-138">Example</span></span>           | <span data-ttu-id="6fb84-139">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="6fb84-139">Example Matches</span></span>                                                                  | <span data-ttu-id="6fb84-140">固定条件</span><span class="sxs-lookup"><span data-stu-id="6fb84-140">Invariant</span></span><br><span data-ttu-id="6fb84-141">区域性</span><span class="sxs-lookup"><span data-stu-id="6fb84-141">culture</span></span><br><span data-ttu-id="6fb84-142">匹配</span><span class="sxs-lookup"><span data-stu-id="6fb84-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="6fb84-143">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="6fb84-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="6fb84-144">No</span><span class="sxs-lookup"><span data-stu-id="6fb84-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="6fb84-145">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="6fb84-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="6fb84-146">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="6fb84-147">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="6fb84-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="6fb84-148">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="6fb84-149">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6fb84-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6fb84-150">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="6fb84-151">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6fb84-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6fb84-152">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="6fb84-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="6fb84-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="6fb84-154">No</span><span class="sxs-lookup"><span data-stu-id="6fb84-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="6fb84-155">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6fb84-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6fb84-156">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="6fb84-157">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6fb84-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6fb84-158">是</span><span class="sxs-lookup"><span data-stu-id="6fb84-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="6fb84-159">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="6fb84-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="6fb84-160">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="6fb84-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="6fb84-161">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="6fb84-161">NavLink component</span></span>

<span data-ttu-id="6fb84-162">创建导航链接时, 请使用`<a>`组件来代替HTMLhyperlink元素()。`NavLink`</span><span class="sxs-lookup"><span data-stu-id="6fb84-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="6fb84-163">组件`NavLink`的行为`<a>`类似于元素`active` , 只不过它会根据 CSS 类是否`href`与当前 URL 匹配来进行切换。</span><span class="sxs-lookup"><span data-stu-id="6fb84-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="6fb84-164">`active`类可帮助用户了解在显示的导航链接中哪一页是活动页。</span><span class="sxs-lookup"><span data-stu-id="6fb84-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="6fb84-165">以下`NavMenu`组件创建演示如何使用`NavLink`组件的[启动](https://getbootstrap.com/docs/)导航栏:</span><span class="sxs-lookup"><span data-stu-id="6fb84-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="6fb84-166">可以将两`NavLinkMatch`个选项分配`Match`给该`<NavLink>`元素的属性:</span><span class="sxs-lookup"><span data-stu-id="6fb84-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="6fb84-167">`NavLinkMatch.All`当与整个当前 URL 匹配时,处于活动状态。`NavLink` &ndash;</span><span class="sxs-lookup"><span data-stu-id="6fb84-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="6fb84-168">`NavLinkMatch.Prefix`(*默认值*)&ndash; 当与当前URL的任何前缀匹配时,`NavLink`将处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="6fb84-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="6fb84-169">在前面的示例中, home `NavLink` `href=""`与 home `active` URL 匹配, 仅在应用的默认基路径 URL (例如, `https://localhost:5001/`) 中接收 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="6fb84-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="6fb84-170">当用户`NavLink`使用`MyComponent`前缀`active` (例如`https://localhost:5001/MyComponent` 和`https://localhost:5001/MyComponent/AnotherSegment`) 访问任何 URL 时, 第二个会接收该类。</span><span class="sxs-lookup"><span data-stu-id="6fb84-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="6fb84-171">其他`NavLink`组件特性会传递到呈现的定位点标记。</span><span class="sxs-lookup"><span data-stu-id="6fb84-171">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="6fb84-172">在下面的示例中, `NavLink`组件`target`包含特性:</span><span class="sxs-lookup"><span data-stu-id="6fb84-172">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="6fb84-173">呈现以下 HTML 标记:</span><span class="sxs-lookup"><span data-stu-id="6fb84-173">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="6fb84-174">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="6fb84-174">URI and navigation state helpers</span></span>

<span data-ttu-id="6fb84-175">使用`Microsoft.AspNetCore.Components.IUriHelper`在代码中C#处理 uri 和导航。</span><span class="sxs-lookup"><span data-stu-id="6fb84-175">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="6fb84-176">`IUriHelper`提供下表中显示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="6fb84-176">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="6fb84-177">成员</span><span class="sxs-lookup"><span data-stu-id="6fb84-177">Member</span></span> | <span data-ttu-id="6fb84-178">描述</span><span class="sxs-lookup"><span data-stu-id="6fb84-178">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="6fb84-179">获取当前的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6fb84-179">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="6fb84-180">获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI (带有尾随斜杠)。</span><span class="sxs-lookup"><span data-stu-id="6fb84-180">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="6fb84-181">通常, `GetBaseUri`与*wwwroot/index.html* ( `href` Blazor 客户端) 或`<base>` *Pages/_Host* (Blazor 服务器端) 中文档的元素的属性相对应。</span><span class="sxs-lookup"><span data-stu-id="6fb84-181">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="6fb84-182">定位到指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="6fb84-182">Navigates to the specified URI.</span></span> <span data-ttu-id="6fb84-183">如果`forceLoad`为`true`:</span><span class="sxs-lookup"><span data-stu-id="6fb84-183">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="6fb84-184">客户端路由被绕过。</span><span class="sxs-lookup"><span data-stu-id="6fb84-184">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="6fb84-185">无论 URI 是否通常由客户端路由器处理, 浏览器都被迫从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="6fb84-185">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="6fb84-186">导航位置发生更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="6fb84-186">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="6fb84-187">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6fb84-187">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="6fb84-188">给定基 uri (例如, 先前由返回的`GetBaseUri`uri), 会将绝对 uri 转换为相对于基 uri 前缀的 uri。</span><span class="sxs-lookup"><span data-stu-id="6fb84-188">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="6fb84-189">选择该按钮时, 以下组件将`Counter`导航到应用的组件:</span><span class="sxs-lookup"><span data-stu-id="6fb84-189">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
