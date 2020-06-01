---
<span data-ttu-id="8b8c9-101">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-102">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-103">'Identity'</span></span>
- <span data-ttu-id="8b8c9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-105">'Razor'</span></span>
- <span data-ttu-id="8b8c9-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="8b8c9-107">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="8b8c9-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="8b8c9-108">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8b8c9-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8b8c9-109">了解如何路由请求，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件在 Blazor 应用中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="8b8c9-110">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="8b8c9-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="8b8c9-111"> 服务器已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="8b8c9-112">ASP.NET Core 应用配置为接受 `Startup.Configure` 中带有 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 的交互式组件的传入连接：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="8b8c9-113">最典型的配置是将所有请求路由到 Razor 页面，该页面为 Blazor 服务器应用充当服务器端的主机。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="8b8c9-114">按照约定，*主机*页通常命名为 *_Host.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="8b8c9-115">主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以较低的优先级运行。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="8b8c9-116">其他路由不匹配时，会考虑回退路由。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="8b8c9-117">这让应用能够使用其他控制器和页面，而不会干扰 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="8b8c9-118">路由模板</span><span class="sxs-lookup"><span data-stu-id="8b8c9-118">Route templates</span></span>

<span data-ttu-id="8b8c9-119"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件可实现到具有指定路由的每个组件的路由。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="8b8c9-120"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件出现在 *App.razor* 文件中：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="8b8c9-121">编译带有 `@page` 指令的 *.razor* 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Components.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="8b8c9-122">在运行时，<xref:Microsoft.AspNetCore.Components.RouteView> 组件：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="8b8c9-123">从 <xref:Microsoft.AspNetCore.Components.Routing.Router> 接收 <xref:Microsoft.AspNetCore.Components.RouteData> 以及任何所需的参数。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="8b8c9-124">通过指定参数使用指定组件的布局（或可选的默认布局）呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="8b8c9-125">可选择使用布局类指定 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 参数，以用于未指定布局的组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="8b8c9-126">默认的 Blazor 模板指定 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="8b8c9-127">*MainLayout.razor* 在模板项目的 *Shared* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="8b8c9-128">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="8b8c9-129">可将多个路由模板应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="8b8c9-130">以下组件响应对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="8b8c9-131">若要正确解析 URL，应用必须在其 wwwroot/index.html 文件 (Blazor WebAssembly) 或 Pages/_Host.cshtml 文件 (Blazor Server) 中包括 `<base>` 标记，并在 `href` 属性 (`<base href="/">`) 中指定应用基路径。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="8b8c9-132">有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="8b8c9-133">在找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="8b8c9-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="8b8c9-134">如果找不到所请求路由的内容，则 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="8b8c9-135">在 *App.razor* 文件中，在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板参数中设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="8b8c9-136">`<NotFound>` 标记的内容可以包括任意项，例如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="8b8c9-137">若要将默认布局应用于 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="8b8c9-138">从多个程序集路由到组件</span><span class="sxs-lookup"><span data-stu-id="8b8c9-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="8b8c9-139">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 参数为 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件指定搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="8b8c9-140">除 `AppAssembly` 指定的程序集外，还要考虑指定的程序集。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="8b8c9-141">在以下示例中，`Component1` 是在引用的类库中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="8b8c9-142">以下 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 示例为 `Component1` 提供路由支持：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="8b8c9-143">路由参数</span><span class="sxs-lookup"><span data-stu-id="8b8c9-143">Route parameters</span></span>

<span data-ttu-id="8b8c9-144">路由器使用路由参数以相同的名称填充相应的组件参数（不区分大小写）：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="8b8c9-145">不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="8b8c9-146">上一个示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="8b8c9-147">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="8b8c9-148">第二个 `@page` 指令采用 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="8b8c9-149">路由约束</span><span class="sxs-lookup"><span data-stu-id="8b8c9-149">Route constraints</span></span>

<span data-ttu-id="8b8c9-150">路由约束强制在路由段和组件之间进行类型匹配。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="8b8c9-151">在以下示例中，到 `Users` 组件的路由仅在以下情况下匹配：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="8b8c9-152">请求 URL 上存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="8b8c9-153">`Id` 段是整数 (`int`)。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="8b8c9-154">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="8b8c9-155">有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="8b8c9-156">约束</span><span class="sxs-lookup"><span data-stu-id="8b8c9-156">Constraint</span></span> | <span data-ttu-id="8b8c9-157">示例</span><span class="sxs-lookup"><span data-stu-id="8b8c9-157">Example</span></span>           | <span data-ttu-id="8b8c9-158">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="8b8c9-158">Example Matches</span></span>                                                                  | <span data-ttu-id="8b8c9-159">固定条件</span><span class="sxs-lookup"><span data-stu-id="8b8c9-159">Invariant</span></span><br><span data-ttu-id="8b8c9-160">区域性</span><span class="sxs-lookup"><span data-stu-id="8b8c9-160">culture</span></span><br><span data-ttu-id="8b8c9-161">匹配</span><span class="sxs-lookup"><span data-stu-id="8b8c9-161">matching</span></span> |
| ---
<span data-ttu-id="8b8c9-162">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-163">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-164">'Identity'</span></span>
- <span data-ttu-id="8b8c9-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-166">'Razor'</span></span>
- <span data-ttu-id="8b8c9-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-168">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-169">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-170">'Identity'</span></span>
- <span data-ttu-id="8b8c9-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-172">'Razor'</span></span>
- <span data-ttu-id="8b8c9-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-174">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-175">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-176">'Identity'</span></span>
- <span data-ttu-id="8b8c9-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-178">'Razor'</span></span>
- <span data-ttu-id="8b8c9-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-179">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-180">----- | --- title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-181">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-182">'Identity'</span></span>
- <span data-ttu-id="8b8c9-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-184">'Razor'</span></span>
- <span data-ttu-id="8b8c9-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-186">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-187">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-188">'Identity'</span></span>
- <span data-ttu-id="8b8c9-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-190">'Razor'</span></span>
- <span data-ttu-id="8b8c9-191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-192">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-193">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-194">'Identity'</span></span>
- <span data-ttu-id="8b8c9-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-196">'Razor'</span></span>
- <span data-ttu-id="8b8c9-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-198">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-199">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-200">'Identity'</span></span>
- <span data-ttu-id="8b8c9-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-202">'Razor'</span></span>
- <span data-ttu-id="8b8c9-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-204">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-205">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-206">'Identity'</span></span>
- <span data-ttu-id="8b8c9-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-208">'Razor'</span></span>
- <span data-ttu-id="8b8c9-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-210">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-211">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-212">'Identity'</span></span>
- <span data-ttu-id="8b8c9-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-214">'Razor'</span></span>
- <span data-ttu-id="8b8c9-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-215">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-216">--------- | --- title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-217">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-218">'Identity'</span></span>
- <span data-ttu-id="8b8c9-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-220">'Razor'</span></span>
- <span data-ttu-id="8b8c9-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-222">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-223">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-224">'Identity'</span></span>
- <span data-ttu-id="8b8c9-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-226">'Razor'</span></span>
- <span data-ttu-id="8b8c9-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-228">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-229">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-230">'Identity'</span></span>
- <span data-ttu-id="8b8c9-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-232">'Razor'</span></span>
- <span data-ttu-id="8b8c9-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-234">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-235">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-236">'Identity'</span></span>
- <span data-ttu-id="8b8c9-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-238">'Razor'</span></span>
- <span data-ttu-id="8b8c9-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-240">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-241">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-242">'Identity'</span></span>
- <span data-ttu-id="8b8c9-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-244">'Razor'</span></span>
- <span data-ttu-id="8b8c9-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-246">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-247">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-248">'Identity'</span></span>
- <span data-ttu-id="8b8c9-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-250">'Razor'</span></span>
- <span data-ttu-id="8b8c9-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-252">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-253">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-254">'Identity'</span></span>
- <span data-ttu-id="8b8c9-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-256">'Razor'</span></span>
- <span data-ttu-id="8b8c9-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-258">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-259">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-260">'Identity'</span></span>
- <span data-ttu-id="8b8c9-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-262">'Razor'</span></span>
- <span data-ttu-id="8b8c9-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-264">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-265">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-266">'Identity'</span></span>
- <span data-ttu-id="8b8c9-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-268">'Razor'</span></span>
- <span data-ttu-id="8b8c9-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-270">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-271">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-272">'Identity'</span></span>
- <span data-ttu-id="8b8c9-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-274">'Razor'</span></span>
- <span data-ttu-id="8b8c9-275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-276">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-277">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-278">'Identity'</span></span>
- <span data-ttu-id="8b8c9-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-280">'Razor'</span></span>
- <span data-ttu-id="8b8c9-281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-282">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-283">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-284">'Identity'</span></span>
- <span data-ttu-id="8b8c9-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-286">'Razor'</span></span>
- <span data-ttu-id="8b8c9-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-288">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-289">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-290">'Identity'</span></span>
- <span data-ttu-id="8b8c9-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-292">'Razor'</span></span>
- <span data-ttu-id="8b8c9-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-294">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-295">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-296">'Identity'</span></span>
- <span data-ttu-id="8b8c9-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-298">'Razor'</span></span>
- <span data-ttu-id="8b8c9-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-300">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-301">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-302">'Identity'</span></span>
- <span data-ttu-id="8b8c9-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-304">'Razor'</span></span>
- <span data-ttu-id="8b8c9-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-306">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-307">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-308">'Identity'</span></span>
- <span data-ttu-id="8b8c9-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-310">'Razor'</span></span>
- <span data-ttu-id="8b8c9-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-312">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-313">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-314">'Identity'</span></span>
- <span data-ttu-id="8b8c9-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-316">'Razor'</span></span>
- <span data-ttu-id="8b8c9-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-318">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-319">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-320">'Identity'</span></span>
- <span data-ttu-id="8b8c9-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-322">'Razor'</span></span>
- <span data-ttu-id="8b8c9-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-324">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-325">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-326">'Identity'</span></span>
- <span data-ttu-id="8b8c9-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-328">'Razor'</span></span>
- <span data-ttu-id="8b8c9-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-330">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-331">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-332">'Identity'</span></span>
- <span data-ttu-id="8b8c9-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-334">'Razor'</span></span>
- <span data-ttu-id="8b8c9-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-336">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-337">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-338">'Identity'</span></span>
- <span data-ttu-id="8b8c9-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-340">'Razor'</span></span>
- <span data-ttu-id="8b8c9-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-342">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-343">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-344">'Identity'</span></span>
- <span data-ttu-id="8b8c9-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-346">'Razor'</span></span>
- <span data-ttu-id="8b8c9-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-348">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-349">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-350">'Identity'</span></span>
- <span data-ttu-id="8b8c9-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-352">'Razor'</span></span>
- <span data-ttu-id="8b8c9-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-354">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-355">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-356">'Identity'</span></span>
- <span data-ttu-id="8b8c9-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-358">'Razor'</span></span>
- <span data-ttu-id="8b8c9-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-360">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-361">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-362">'Identity'</span></span>
- <span data-ttu-id="8b8c9-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-364">'Razor'</span></span>
- <span data-ttu-id="8b8c9-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-366">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-367">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-368">'Identity'</span></span>
- <span data-ttu-id="8b8c9-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-370">'Razor'</span></span>
- <span data-ttu-id="8b8c9-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-372">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-373">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-374">'Identity'</span></span>
- <span data-ttu-id="8b8c9-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-376">'Razor'</span></span>
- <span data-ttu-id="8b8c9-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-378">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-379">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-380">'Identity'</span></span>
- <span data-ttu-id="8b8c9-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-382">'Razor'</span></span>
- <span data-ttu-id="8b8c9-383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-384">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-385">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-386">'Identity'</span></span>
- <span data-ttu-id="8b8c9-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-388">'Razor'</span></span>
- <span data-ttu-id="8b8c9-389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-390">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-391">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-392">'Identity'</span></span>
- <span data-ttu-id="8b8c9-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-394">'Razor'</span></span>
- <span data-ttu-id="8b8c9-395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-396">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-397">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-398">'Identity'</span></span>
- <span data-ttu-id="8b8c9-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-400">'Razor'</span></span>
- <span data-ttu-id="8b8c9-401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-402">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-403">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-404">'Identity'</span></span>
- <span data-ttu-id="8b8c9-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-406">'Razor'</span></span>
- <span data-ttu-id="8b8c9-407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-408">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-409">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-410">'Identity'</span></span>
- <span data-ttu-id="8b8c9-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-412">'Razor'</span></span>
- <span data-ttu-id="8b8c9-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-414">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-415">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-416">'Identity'</span></span>
- <span data-ttu-id="8b8c9-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-418">'Razor'</span></span>
- <span data-ttu-id="8b8c9-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-420">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-421">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-422">'Identity'</span></span>
- <span data-ttu-id="8b8c9-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-424">'Razor'</span></span>
- <span data-ttu-id="8b8c9-425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-426">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-427">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-428">'Identity'</span></span>
- <span data-ttu-id="8b8c9-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-430">'Razor'</span></span>
- <span data-ttu-id="8b8c9-431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-432">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-433">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-434">'Identity'</span></span>
- <span data-ttu-id="8b8c9-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-436">'Razor'</span></span>
- <span data-ttu-id="8b8c9-437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-438">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-439">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-440">'Identity'</span></span>
- <span data-ttu-id="8b8c9-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-442">'Razor'</span></span>
- <span data-ttu-id="8b8c9-443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-443">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-444">---------------------------------------- | :--- title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-445">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-446">'Identity'</span></span>
- <span data-ttu-id="8b8c9-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-448">'Razor'</span></span>
- <span data-ttu-id="8b8c9-449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-450">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-451">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-452">'Identity'</span></span>
- <span data-ttu-id="8b8c9-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-454">'Razor'</span></span>
- <span data-ttu-id="8b8c9-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-456">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-457">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-458">'Identity'</span></span>
- <span data-ttu-id="8b8c9-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-460">'Razor'</span></span>
- <span data-ttu-id="8b8c9-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-462">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-463">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-464">'Identity'</span></span>
- <span data-ttu-id="8b8c9-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-466">'Razor'</span></span>
- <span data-ttu-id="8b8c9-467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-468">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-469">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-470">'Identity'</span></span>
- <span data-ttu-id="8b8c9-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-472">'Razor'</span></span>
- <span data-ttu-id="8b8c9-473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-474">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-475">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-476">'Identity'</span></span>
- <span data-ttu-id="8b8c9-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-478">'Razor'</span></span>
- <span data-ttu-id="8b8c9-479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-480">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-481">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-482">'Identity'</span></span>
- <span data-ttu-id="8b8c9-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-484">'Razor'</span></span>
- <span data-ttu-id="8b8c9-485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-486">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-487">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-488">'Identity'</span></span>
- <span data-ttu-id="8b8c9-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-490">'Razor'</span></span>
- <span data-ttu-id="8b8c9-491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-492">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-493">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-494">'Identity'</span></span>
- <span data-ttu-id="8b8c9-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-496">'Razor'</span></span>
- <span data-ttu-id="8b8c9-497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-498">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-499">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-500">'Identity'</span></span>
- <span data-ttu-id="8b8c9-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-502">'Razor'</span></span>
- <span data-ttu-id="8b8c9-503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-504">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-505">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-506">'Identity'</span></span>
- <span data-ttu-id="8b8c9-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-508">'Razor'</span></span>
- <span data-ttu-id="8b8c9-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-510">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-511">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-512">'Identity'</span></span>
- <span data-ttu-id="8b8c9-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-514">'Razor'</span></span>
- <span data-ttu-id="8b8c9-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-516">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-517">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-518">'Identity'</span></span>
- <span data-ttu-id="8b8c9-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-520">'Razor'</span></span>
- <span data-ttu-id="8b8c9-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-521">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 否                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 是                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 是                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 是                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 是                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 否                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 是                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 是                              |</span><span class="sxs-lookup"><span data-stu-id="8b8c9-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="8b8c9-523">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 <xref:System.DateTime>）。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="8b8c9-524">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="8b8c9-525">使用包含点的 URL 进行路由</span><span class="sxs-lookup"><span data-stu-id="8b8c9-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="8b8c9-526">在 Blazor 服务器应用中，_Host.cshtml 中的默认路由为 `/` (`@page "/"`)。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="8b8c9-527">包含点 (`.`) 的请求 URL 与默认路由不匹配，因为 URL 似乎在请求文件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="8b8c9-528">Blazor 应用针对不存在的静态文件返回“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="8b8c9-529">若要使用包含点的路由，请使用以下路由模板配置 *_Host.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="8b8c9-530">`"/{**path}"` 模板包括：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="8b8c9-531">双星号 *catch-all* 语法 (`**`)，用于捕获跨多个文件夹边界的路径，而无需编码正斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="8b8c9-532">`path` 路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="8b8c9-533">Razor 组件 (.razor) 不支持 Catch-all 参数语法 (`*`/`**`)。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="8b8c9-534">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="8b8c9-535">NavLink 组件</span><span class="sxs-lookup"><span data-stu-id="8b8c9-535">NavLink component</span></span>

<span data-ttu-id="8b8c9-536">创建导航链接时，请使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件代替 HTML 超链接元素 (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="8b8c9-537"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件的行为方式类似于 `<a>` 元素，但它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="8b8c9-538">`active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="8b8c9-539">以下 `NavMenu` 组件创建[启动](https://getbootstrap.com/docs/)导航栏，该导航栏演示如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="8b8c9-540">有两个 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 选项可分配给 `<NavLink>` 元素的 `Match` 属性：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="8b8c9-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前整个 URL 匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="8b8c9-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>（默认）：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前 URL 的任何前缀匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="8b8c9-543">在前面的示例中，主页 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`https://localhost:5001/`）处接收 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="8b8c9-544">当用户访问带有 `MyComponent` 前缀的任何 URL（例如，`https://localhost:5001/MyComponent` 和 `https://localhost:5001/MyComponent/AnotherSegment`）时，第二个 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 接收 `active` 类。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="8b8c9-545">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件属性会传递到呈现的定位标记。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="8b8c9-546">在以下示例中，<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件包括 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="8b8c9-547">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="8b8c9-548">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="8b8c9-548">URI and navigation state helpers</span></span>

<span data-ttu-id="8b8c9-549">在 C# 代码中将 <xref:Microsoft.AspNetCore.Components.NavigationManager> 与 URI 和导航配合使用。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="8b8c9-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="8b8c9-551">成员</span><span class="sxs-lookup"><span data-stu-id="8b8c9-551">Member</span></span> | <span data-ttu-id="8b8c9-552">描述</span><span class="sxs-lookup"><span data-stu-id="8b8c9-552">Description</span></span> |
| ---
<span data-ttu-id="8b8c9-553">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-554">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-555">'Identity'</span></span>
- <span data-ttu-id="8b8c9-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-557">'Razor'</span></span>
- <span data-ttu-id="8b8c9-558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-558">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-559">--- | --- title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-560">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-561">'Identity'</span></span>
- <span data-ttu-id="8b8c9-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-563">'Razor'</span></span>
- <span data-ttu-id="8b8c9-564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-565">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-566">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-567">'Identity'</span></span>
- <span data-ttu-id="8b8c9-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-569">'Razor'</span></span>
- <span data-ttu-id="8b8c9-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b8c9-571">title:“ASP.NET Core Blazor 路由”author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b8c9-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-572">'Blazor'</span></span>
- <span data-ttu-id="8b8c9-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-573">'Identity'</span></span>
- <span data-ttu-id="8b8c9-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b8c9-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b8c9-575">'Razor'</span></span>
- <span data-ttu-id="8b8c9-576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b8c9-576">'SignalR' uid:</span></span> 

<span data-ttu-id="8b8c9-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 获取当前绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="8b8c9-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 获取可作为相对 URI 路径前缀的基 URI（带有尾随斜线），以生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="8b8c9-579">通常，<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 对应于 *wwwroot/index.html* (Blazor WebAssembly) 或 *Pages/_Host.cshtml* (Blazor Server) 中文档的 `<base>` 元素上的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="8b8c9-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 转到指定 URI。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="8b8c9-581">如果 `forceLoad` 为 `true`，则：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="8b8c9-582">客户端路由会被绕过。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="8b8c9-583">无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="8b8c9-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | 当导航位置改变时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="8b8c9-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="8b8c9-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | 给定基 URI（例如，之前由 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="8b8c9-587">选择该按钮后，以下组件导航到应用的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="8b8c9-588">以下组件通过设置 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 来处理位置改变事件。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8b8c9-589">在框架调用 `Dispose` 时，解除挂接 `HandleLocationChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="8b8c9-590">解除挂接该方法可允许组件进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="8b8c9-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 可提供以下有关该事件的信息：</span><span class="sxs-lookup"><span data-stu-id="8b8c9-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="8b8c9-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="8b8c9-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果为 `true`，则 Blazor 拦截了浏览器中的导航。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="8b8c9-594">如果为 `false`，则 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 导致了导航发生。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="8b8c9-595">要详细了解组件处置，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="8b8c9-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
