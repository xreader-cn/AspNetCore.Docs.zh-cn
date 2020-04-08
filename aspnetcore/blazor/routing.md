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
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 路由

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

了解如何路由请求以及如何使用 `NavLink` 组件在 Blazor 应用中创建导航链接。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 终结点路由集成

Blazor Server 已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。 ASP.NET Core 应用配置为接受 `MapBlazorHub` 中带有 `Startup.Configure` 的交互式组件的传入连接：

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最典型的配置是将所有请求路由到 Razor 页面，该页面充当 Blazor Server 应用服务器端部分的主机。 按照约定，*主机*页通常命名为 *_Host.cshtml*。 主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以较低的优先级运行。 其他路由不匹配时，会考虑回退路由。 这让应用能够使用其他控制器和页面，而不会干扰 Blazor Server 应用。

## <a name="route-templates"></a>路由模板

`Router` 组件可实现到具有指定路由的每个组件的路由。 `Router` 组件出现在 *App.razor* 文件中：

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

编译带有 *指令的*.razor`@page` 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Components.RouteAttribute>。

在运行时，`RouteView` 组件：

* 从 `RouteData` 接收 `Router` 以及任何所需的参数。
* 通过指定参数使用指定组件的布局（或可选的默认布局）呈现该组件。

可选择使用布局类指定 `DefaultLayout` 参数，以用于未指定布局的组件。 默认的 Blazor 模板指定 `MainLayout` 组件。 *MainLayout.razor* 在模板项目的 *Shared* 文件夹中。 有关布局的详细信息，请参阅 <xref:blazor/layouts>。

可将多个路由模板应用于一个组件。 以下组件响应对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> 若要正确解析 URL，应用必须在其 `<base>`wwwroot/index.html*文件 (Blazor WebAssembly) 或*Pages/_Host.cshtml*文件 (Blazor Server) 中包括* 标记，并使用 `href` 属性 (`<base href="/">`) 中指定的应用基路径。 有关更多信息，请参见 <xref:host-and-deploy/blazor/index#app-base-path>。

## <a name="provide-custom-content-when-content-isnt-found"></a>在找不到内容时提供自定义内容

如果找不到所请求路由的内容，则 `Router` 组件允许应用指定自定义内容。

在 *App.razor* 文件中，在 `NotFound` 组件的 `Router` 模板参数中设置自定义内容：

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

`<NotFound>` 标记的内容可以包括任意项，例如其他交互式组件。 若要将默认布局应用于 `NotFound` 内容，请参阅 <xref:blazor/layouts>。

## <a name="route-to-components-from-multiple-assemblies"></a>从多个程序集路由到组件

使用 `AdditionalAssemblies` 参数为 `Router` 组件指定搜索可路由组件时要考虑的其他程序集。 除 `AppAssembly` 指定的程序集外，还要考虑指定的程序集。 在以下示例中，`Component1` 是在引用的类库中定义的可路由组件。 以下 `AdditionalAssemblies` 示例为 `Component1` 提供路由支持：

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>路由参数

路由器使用路由参数以相同的名称填充相应的组件参数（不区分大小写）：

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

不支持可选参数。 上一个示例中应用了两个 `@page` 指令。 第一个指令允许导航到没有参数的组件。 第二个 `@page` 指令采用 `{text}` 路由参数，并将值赋予 `Text` 属性。

## <a name="route-constraints"></a>路由约束

路由约束强制在路由段和组件之间进行类型匹配。

在以下示例中，到 `Users` 组件的路由仅在以下情况下匹配：

* 请求 URL 上存在 `Id` 路由段。
* `Id` 段是整数 (`int`)。

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

下表中显示的路由约束可用。 有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。

| 约束 | 示例           | 匹配项示例                                                                  | 固定条件<br>区域性<br>匹配 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`， `FALSE`                                                                  | No                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`， `2016-12-31 7:32pm`                                                | 是                              |
| `decimal`  | `{price:decimal}` | `49.99`， `-1,000.01`                                                             | 是                              |
| `double`   | `{weight:double}` | `1.234`， `-1,001.01e8`                                                           | 是                              |
| `float`    | `{weight:float}`  | `1.234`， `-1,001.01e8`                                                           | 是                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               |
| `int`      | `{id:int}`        | `123456789`， `-123456789`                                                        | 是                              |
| `long`     | `{ticks:long}`    | `123456789`， `-123456789`                                                        | 是                              |

> [!WARNING]
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。 这些约束假定 URL 不可本地化。

### <a name="routing-with-urls-that-contain-dots"></a>使用包含点的 URL 进行路由

在 Blazor Server 应用中， *_Host.cshtml* 中的默认路由为 `/` (`@page "/"`)。 包含点 (`.`) 的请求 URL 与默认路由不匹配，因为 URL 似乎在请求文件。 Blazor 应用针对不存在的静态文件返回 *404 - 未找到*响应。 若要使用包含点的路由，请使用以下路由模板配置 *_Host.cshtml*：

```cshtml
@page "/{**path}"
```

`"/{**path}"` 模板包括：

* 双星号 *catch-all* 语法 (`**`)，用于捕获跨多个文件夹边界的路径，而无需编码正斜杠 (`/`)。
* `path` 路由参数名称。

> [!NOTE]
> Razor 组件 ( *.razor*) `*`不/支持 `**`catch-all **参数语法 (**  )。

有关更多信息，请参见 <xref:fundamentals/routing>。

## <a name="navlink-component"></a>NavLink 组件

创建导航链接时，请使用 `NavLink` 组件代替 HTML 超链接元素 (`<a>`)。 `NavLink` 组件的行为方式类似于 `<a>` 元素，但它根据其 `active` 是否与当前 URL 匹配来切换 `href` CSS 类。 `active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。

以下 `NavMenu` 组件创建[启动](https://getbootstrap.com/docs/)导航栏，该导航栏演示如何使用 `NavLink` 组件：

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

有两个 `NavLinkMatch` 选项可分配给 `Match` 元素的 `<NavLink>` 属性：

* `NavLinkMatch.All` &ndash; `NavLink` 在与当前整个 URL 匹配的情况下处于活动状态。
* `NavLinkMatch.Prefix`（*默认*）&ndash; `NavLink` 在与当前 URL 的任何前缀匹配的情况下处于活动状态。

在前面的示例中，主页 `NavLink` `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`active`）处接收 `https://localhost:5001/` CSS 类。 当用户访问带有 `NavLink` 前缀的任何 URL（例如，`active` 和 `MyComponent`）时，第二个 `https://localhost:5001/MyComponent` 接收 `https://localhost:5001/MyComponent/AnotherSegment` 类。

其他 `NavLink` 组件属性会传递到呈现的定位标记。 在以下示例中，`NavLink` 组件包括 `target` 属性：

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

呈现以下 HTML 标记：

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 和导航状态帮助程序

在 C# 代码中将 <xref:Microsoft.AspNetCore.Components.NavigationManager> 与 URI 和导航配合使用。 `NavigationManager` 提供下表所示的事件和方法。

| 成员 | 描述 |
| ------ | ----------- |
| URI | 获取当前绝对 URI。 |
| BaseUri | 获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。 通常，`BaseUri` 对应于 `href`wwwroot/index.html`<base>` (*WebAssembly) 或*Pages/_Host.cshtmlBlazor (*Server) 中文档的* 元素上的 Blazor 属性。 |
| NavigateTo | 导航到指定 URI。 如果 `forceLoad` 为 `true`，则：<ul><li>客户端路由会被绕过。</li><li>无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</li></ul> |
| LocationChanged | 导航位置更改时触发的事件。 |
| ToAbsoluteUri | 将相对 URI 转换为绝对 URI。 |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | 给定基 URI（例如，之前由 `GetBaseUri` 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。 |

选择该按钮后，以下组件导航到应用的 `Counter` 组件：

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

以下组件处理位置更改事件。 在框架调用 `HandleLocationChanged` 时，解除挂接 `Dispose` 方法。 解除挂接该方法可允许组件进行垃圾回收。

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

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 可提供以下有关该事件的信息：

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 新位置的 URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; 如果为 `true`，则 Blazor 截获了浏览器中的导航。 如果为 `false`，则 [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) 导致发生导航。

要详细了解组件处置，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。
