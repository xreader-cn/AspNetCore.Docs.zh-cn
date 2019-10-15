---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何在应用中路由请求，以及如何在 NavLink 组件中路由请求。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2019
uid: blazor/routing
ms.openlocfilehash: 8f48112237e6dd3fed88404c53b8d7d9137ef6ff
ms.sourcegitcommit: 0b8a7571bf7acf85bf16118acb2435001cbe4b5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72236533"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 路由

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

了解如何路由请求，以及如何使用 `NavLink` 组件在 Blazor 应用程序中创建导航链接。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 终结点路由集成

Blazor 服务器已集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。 ASP.NET Core 应用配置为接受 `Startup.Configure` 中 `MapBlazorHub` 为0的交互式组件的传入连接：

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最典型的配置是将所有请求路由到 Razor 页，该页面充当 Blazor 服务器应用的服务器端部分的主机。 按照约定，*主机*页通常名为 *_Host*。 主机文件中指定的路由称为*回退路由*，因为它在路由匹配中以低优先级操作。 当其他路由不匹配时，将考虑回退路由。 这允许应用使用其他控制器和页面，而不会干扰 Blazor Server 应用。

## <a name="route-templates"></a>路由模板

`Router` 组件允许路由到具有指定路由的每个组件。 `Router` 组件将出现在*app.config*文件中：

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

编译带有 `@page` 指令的*razor*文件时，将提供生成的类，<xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板。

在运行时，`RouteView` 的组件：

* 接收来自 `Router` 的 `RouteData`，以及任何所需的参数。
* 使用指定的参数呈现指定组件及其布局（或可选默认布局）。

您可以选择指定一个带有布局类的 `DefaultLayout` 参数，用于未指定布局的组件。 默认的 Blazor 模板指定 `MainLayout` 组件。 *MainLayout*位于模板项目的*共享*文件夹中。 有关布局的详细信息，请参阅 <xref:blazor/layouts>。

可以将多个路由模板应用于组件。 以下组件响应 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> 若要正确解析 Url，应用必须在其*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/_Host*文件（Blazor 服务器）中包含一个 `<base>` 标记，其中包含 `href` 属性（`<base href="/">`）中指定的应用程序基路径。 有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path> 。

## <a name="provide-custom-content-when-content-isnt-found"></a>当找不到内容时提供自定义内容

如果找不到请求的路由的内容，则 `Router` 的组件允许应用指定自定义内容。

在*app.config*文件中，在 `Router` 组件的 `NotFound` 模板参数中设置自定义内容：

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

`<NotFound>` 标记的内容可以包含任意项，例如其他交互组件。 若要将默认布局应用于 `NotFound` 内容，请参阅 <xref:blazor/layouts>。

## <a name="route-to-components-from-multiple-assemblies"></a>从多个程序集中路由到组件

使用 `AdditionalAssemblies` 参数为在搜索可路由组件时要考虑的 `Router` 组件指定其他程序集。 除了 `AppAssembly` 旁1/-0 指定的程序集，还会考虑指定的程序集。 在下面的示例中，`Component1` 是在引用的类库中定义的可路由组件。 以下 `AdditionalAssemblies` 为 `Component1` 的示例将导致路由支持-1：

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }>
    ...
</Router>
```

## <a name="route-parameters"></a>路由参数

路由器使用路由参数来填充具有相同名称（不区分大小写）的相应组件参数：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

ASP.NET Core 3.0 中的 Blazor 应用不支持可选参数。 前面的示例中应用了两个 `@page` 指令。 第一个允许导航到没有参数的组件。 第二个 `@page` 指令采用 `{text}` 的路由参数，并将该值分配给 `Text`。

## <a name="route-constraints"></a>路由约束

路由约束向组件强制执行对路由段的类型匹配。

在下面的示例中，到 `Users` 组件的路由仅在以下情况下才匹配：

* 请求 URL 上存在 `Id` 个路由段。
* `Id` 段是一个整数（`int`）。

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

下表中显示的路由约束可用。 有关与固定区域性匹配的路由约束，请参阅表下面的警告以获取详细信息。

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

### <a name="routing-with-urls-that-contain-dots"></a>带有包含点的 Url 的路由

在 Blazor 服务器应用中， *_Host*中的默认路由 `/` （`@page "/"`）。 由于 URL 显示为请求文件，因此包含点（`.`）的请求 URL 不匹配。 对于不存在的静态文件，Blazor 应用返回 " *404-未找到*" 响应。 若要使用包含点的路由，请使用以下路由模板配置 *_Host* ：

```cshtml
@page "/{**path}"
```

`"/{**path}"` 模板包括：

* 双星号*catch-all*语法（`**`）捕获跨多个文件夹边界的路径，而无需编码正斜杠（`/`）。
* `path` 路由参数名称。

有关详细信息，请参阅 <xref:fundamentals/routing> 。

## <a name="navlink-component"></a>NavLink 组件

创建导航链接时，请使用 `NavLink` 组件来代替 HTML hyperlink 元素（`<a>`）。 `NavLink` 组件的行为类似于 `<a>` 元素，只不过它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。 `active` 类可帮助用户了解在显示的导航链接中哪一页是活动页。

以下`NavMenu`组件创建演示如何使用`NavLink`组件的[启动](https://getbootstrap.com/docs/)导航栏：

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

可以将两个 `NavLinkMatch` 选项分配给 `<NavLink>` 元素的 `Match` 属性：

* `NavLinkMatch.All` &ndash; 如果 `NavLink` 与整个当前 URL 匹配，则为 "活动"。
* `NavLinkMatch.Prefix`（*默认值*） &ndash; 当 `NavLink` 与当前 URL 的任何前缀匹配时，将处于活动状态。

在前面的示例中，Home `NavLink` `href=""` 与 home URL 匹配，并且仅接收应用的默认基路径 URL 中的 `active` CSS 类（例如，`https://localhost:5001/`）。 第二个 `NavLink` 会在用户访问具有 `MyComponent` 前缀的任何 URL 时收到 `active` 类（例如，`https://localhost:5001/MyComponent` 和 `https://localhost:5001/MyComponent/AnotherSegment`）。

其他 `NavLink` 组件特性会传递到呈现的定位点标记。 在下面的示例中，`NavLink` 组件包含 `target` 属性：

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

呈现以下 HTML 标记：

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 和导航状态帮助程序

使用 `Microsoft.AspNetCore.Components.NavigationManager` 在代码中C#处理 uri 和导航。 `NavigationManager` 提供下表中显示的事件和方法。

| 成员 | 描述 |
| ------ | ----------- |
| `Uri` | 获取当前的绝对 URI。 |
| `BaseUri` | 获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI （带有尾随斜杠）。 通常，`BaseUri` 为*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）中文档的 `<base>` 元素上的 `href` 属性。 |
| `NavigateTo` | 定位到指定的 URI。 如果 `forceLoad` `true`：<ul><li>客户端路由被绕过。</li><li>无论 URI 是否通常由客户端路由器处理，浏览器都被迫从服务器加载新页面。</li></ul> |
| `LocationChanged` | 导航位置发生更改时触发的事件。 |
| `ToAbsoluteUri` | 将相对 URI 转换为绝对 URI。 |
| `ToBaseRelativePath` | 给定基 URI （例如，以前由 `GetBaseUri` 返回的 URI），会将绝对 URI 转换为相对于基 URI 前缀的 URI。 |

选择该按钮时，以下组件将导航到应用的 `Counter` 组件：

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
