---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何管理应用中的请求路由，以及如何在 Blazor 应用中使用 NavLink 组件进行导航。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279883"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 路由

在本文中，学习如何管理请求路由以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件在 Blazor 应用中创建导航链接。

## <a name="route-templates"></a>路由模板

通过 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件可在 Blazor 应用中路由到 Razor 组件。 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件在 Blazor 应用的 `App` 组件中使用。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

编译带有 [`@page` 指令](xref:mvc/views/razor#page)的 Razor 组件 (`.razor`) 时，将为生成的组件类提供一个 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 来指定组件的路由模板。

当应用启动时，将扫描指定为 路由器的 `AppAssembly` 的程序集，来收集具有 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 的应用组件的路由信息。

在运行时，<xref:Microsoft.AspNetCore.Components.RouteView> 组件：

* 从 <xref:Microsoft.AspNetCore.Components.Routing.Router> 接收 <xref:Microsoft.AspNetCore.Components.RouteData> 以及所有路由参数。
* 使用指定的组件的[布局](xref:blazor/layouts)来呈现该组件，包括任何后续嵌套布局。

对于没有使用 [`@layout` 指令](xref:blazor/layouts#specify-a-layout-in-a-component)指定布局的组件，可选择使用布局类指定一个 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 参数。 框架的 Blazor 项目模板会指定 `MainLayout` 组件 (`Shared/MainLayout.razor`) 作为应用的默认布局。 有关布局的详细信息，请参阅 <xref:blazor/layouts>。

组件支持使用多个 [`@page` 指令](xref:mvc/views/razor#page)的多个路由模板。 以下示例组件会对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求进行加载。

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> 若要正确解析 URL，应用必须在其 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中加入 `<base>` 标记，并在 `href` 属性中指定应用基路径。 有关详细信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。

## <a name="provide-custom-content-when-content-isnt-found"></a>在找不到内容时提供自定义内容

如果找不到所请求路由的内容，则 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件允许应用指定自定义内容。

在 `App` 组件中，在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板中设置自定义内容。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

任意项都可用作 `<NotFound>` 标记的内容，例如其他交互式组件。 若要将默认布局应用于 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容，请参阅 <xref:blazor/layouts#default-layout>。

## <a name="route-to-components-from-multiple-assemblies"></a>从多个程序集路由到组件

使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 参数为 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件指定搜索可路由组件时要考虑的其他程序集。 除了指定至 `AppAssembly` 的程序集外，还会扫描其他程序集。 在以下示例中，`Component1` 是在引用的[组件类库](xref:blazor/components/class-libraries)中定义的可路由组件。 以下 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 示例为 `Component1` 提供路由支持。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a>路由参数

路由器使用路由参数以相同的名称填充相应的[组件参数](xref:blazor/components/index#component-parameters)。 路由参数名不区分大小写。 在下面的示例中，`text` 参数将路由段的值赋给组件的 `Text` 属性。 对 `/RouteParameter/amazing`发出请求时，`<h1>` 标记内容呈现为 `Blazor is amazing!`。

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

支持可选参数。 在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。 如果该段不存在，则将 `Text` 的值设置为 `fantastic`。

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

不支持可选参数。 在下述示例中，应用了两个 [`@page` 指令](xref:mvc/views/razor#page)。 第一个指令允许导航到没有参数的组件。 第二个指令将 `{text}` 路由参数分配给组件的 `Text` 属性。

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

使用 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) 而不是 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods)，以允许应用使用不同的可选参数值导航到同一组件。 根据上述示例，当用户应该能够从 `/RouteParameter` 导航到 `/RouteParameter/amazing` 或从 `/RouteParameter/amazing` 导航到 `/RouteParameter` 时使用 `OnParametersSet`：

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>路由约束

路由约束强制在路由段和组件之间进行类型匹配。

在以下示例中，到 `User` 组件的路由仅在以下情况下匹配：

* 请求 URL 中存在 `Id` 路由段。
* `Id` 段是一个整数 (`int`) 类型。

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

下表中显示的路由约束可用。 有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。

| 约束 | 示例           | 匹配项示例                                                                  | 固定条件<br>区域性<br>匹配 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 否                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 是                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 是                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 是                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 是                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 否                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 是                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 是                              |

> [!WARNING]
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 <xref:System.DateTime>）。 这些约束假定 URL 不可本地化。

## <a name="routing-with-urls-that-contain-dots"></a>使用包含点的 URL 进行路由

对于托管的 Blazor WebAssembly 和 Blazor Server应用，服务器端默认路由模板假定如果请求 URL 的最后一段包含一个点 (`.`)，则请求一个文件。 例如，URL `https://localhost.com:5001/example/some.thing` 由路由器解释为名为 `some.thing` 的文件的请求。 在没有额外配置的情况下，如果 `some.thing` 是指通过 [`@page` 指令](xref:mvc/views/razor#page)路由到一个组件，且 `some.thing` 是一个路由参数值，那么应用将返回“404 - 未找到”响应。 若要使用具有包含点的一个或多个参数的路由，则应用必须使用自定义模板配置该路由。

请考虑下面的 `Example` 组件，它可以从 URL 的最后一段接收路由参数。

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

若要允许托管的 Blazor WebAssembly 解决方案的 `Server` 应用在 `param` 路由参数中使用一个点来路由请求，请添加一个回退文件路由模板，在该模板的 `Startup.Configure` 中包含该可选参数。

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

若要配置 Blazor Server应用，使其在 `param` 路由参数中使用一个点来路由请求，请添加一个回退页面路由模板，该模板具有`Startup.Configure` 中的可选参数。

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

有关详细信息，请参阅 <xref:fundamentals/routing>。

## <a name="catch-all-route-parameters"></a>catch-all 路由参数

::: moniker range=">= aspnetcore-5.0"

组件支持可跨多个文件夹边界捕获路径的 catch-all 路由参数。

Catch-all 路由参数是：

* 以与路由段名称匹配的方式命名。 命名不区分大小写。
* `string` 类型。 框架不提供自动强制转换。
* 位于 URL 的末尾。

`Pages/CatchAll.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

对于具有 `/catch-all/{*pageRoute}` 路由模板的 URL `/catch-all/this/is/a/test`，`PageRoute` 的值设置为 `this/is/a/test`。

对捕获路径的斜杠和段进行解码。 对于 `/catch-all/{*pageRoute}` 的路由模板，URL `/catch-all/this/is/a%2Ftest%2A` 会生成 `this/is/a/test*`。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

ASP.NET Core 5.0 或更高版本中支持 catch-all 路由参数。 有关详细信息，请选择本文的 5.0 版本。

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>URI 和导航状态帮助程序

在 C# 代码中使用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 来来管理 URI 和导航。 <xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。

| 成员 | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 获取当前绝对 URI。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。 通常，<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 对应于 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 中文档的 `<base>` 元素上的 `href` 属性。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 导航到指定 URI。 如果 `forceLoad` 为 `true`，则：<ul><li>客户端路由会被绕过。</li><li>无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | 导航位置更改时触发的事件。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 将相对 URI 转换为绝对 URI。 |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | 给定基 URI（例如，之前由 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。 |

对于 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 事件，<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供了下述导航事件信息：

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果为 `true`，则 Blazor 拦截了浏览器中的导航。 如果为 `false`，则 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 导致了导航发生。

以下组件：

* 使用 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> 选择按钮后，导航到应用的 `Counter` 组件 (`Pages/Counter.razor`)。
* 通过订阅 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 来处理位置更改事件。
  * 在框架调用 `Dispose` 时，解除挂接 `HandleLocationChanged` 方法。 解除挂接该方法可允许组件进行垃圾回收。
  * 选择该按钮时，记录器实现会记录以下信息：

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

## <a name="query-string-and-parse-parameters"></a>查询字符串和分析参数

可从 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> 属性中获取请求的查询字符串：

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

若要分析查询字符串的参数，请执行以下操作：

* 应用可使用 <xref:Microsoft.AspNetCore.WebUtilities> API。 如果该 API 对应用不可用，请在应用的项目文件中针对 [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) 添加一个包引用。
* 在使用 <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> 分析查询字符串后获取值。

以下 `ParseQueryString` 组件示例会分析名为 `ship` 的查询字符串参数键。 例如，URL 查询字符串键值对 `?ship=Tardis` 会捕获 `queryValue` 中的值 `Tardis`。 对于下述示例，请使用 URL `https://localhost:5001/parse-query-string?ship=Tardis` 导航到应用。

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a>`NavLink` 和 `NavMenu` 组件

创建导航链接时，请使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件代替 HTML 超链接元素 (`<a>`)。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件的行为方式类似于 `<a>` 元素，但它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。 `active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。 也可以选择将 CSS 类名分配到 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>，以便在当前路由与 `href` 匹配时将自定义 CSS 类应用到呈现的链接。

以下 `NavMenu` 组件创建 [`Bootstrap`](https://getbootstrap.com/docs/) 导航栏，该导航栏演示如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> `NavMenu` 组件 (`NavMenu.razor`) 是在应用的 `Shared` 文件夹中提供的，而该文件夹是从 Blazor 项目模板生成的。

有两个 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 选项可分配给 `<NavLink>` 元素的 `Match` 属性：

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前整个 URL 匹配的情况下处于活动状态。
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>（默认）：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前 URL 的任何前缀匹配的情况下处于活动状态。

在前面的示例中，主页 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`https://localhost:5001/`）处接收 `active` CSS 类。 当用户访问带有 `component` 前缀的任何 URL（例如，`https://localhost:5001/component` 和 `https://localhost:5001/component/another-segment`）时，第二个 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 接收 `active` 类。

其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件属性会传递到呈现的定位标记。 在以下示例中，<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件包括 `target` 属性：

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

呈现以下 HTML 标记：

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> 由于 Blazor 呈现子内容的方式，如果在 `NavLink`（子）组件的内容中使用递增循环变量，则在 `for` 循环内呈现 `NavLink` 组件需要本地索引变量：
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
> 在[子内容](xref:blazor/components/index#child-content)中使用循环变量的任何子组件（而不仅仅是 `NavLink` 组件）都要求必须在此方案中使用索引变量。
>
> 或者，将 `foreach` 循环用于 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>：
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

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 终结点路由集成

*本部分仅适用于 Blazor Server应用。*

Blazor Server 已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。 ASP.NET Core 应用配置为接受 `Startup.Configure` 中带有 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 的交互式组件的传入连接。

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

典型的配置是将所有请求路由到 Razor 页面，该页面充当 Blazor Server应用的服务器端部分的主机。 按照约定，主机页面通常在应用的 `Pages` 文件夹中被命名为 `_Host.cshtml`。

主机文件中指定的路由称为 *回退路由*，因为它在路由匹配中以较低的优先级运行。 其他路由不匹配时，会使用回退路由。 这让应用能够使用其他控制器和页面，而不会干扰 Blazor Server应用中的组件路由。

若要了解如何为非根 URL 服务器托管配置 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。
