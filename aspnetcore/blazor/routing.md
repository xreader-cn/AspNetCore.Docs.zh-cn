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
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 路由

作者：[Luke Latham](https://github.com/guardrex)

了解如何路由请求，以及如何使用该`NavLink`组件在 Blazor apps 中创建导航链接。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 终结点路由集成

Blazor 服务器端集成到[ASP.NET Core 终结点路由](xref:fundamentals/routing)中。 ASP.NET Core 的应用程序配置为使用`MapBlazorHub`中`Startup.Configure`的交互式组件接受传入连接：

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>路由模板

`Router`组件允许路由到具有指定路由的每个组件。 该`Router`组件将出现在*app.config*文件中：

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

编译包含 `@page`指令的 razor 文件时，将为生成的<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>类提供指定路由模板的。

在运行时， `RouteView`组件：

* 接收来自的`Router` ，以及任何所需的参数。 `RouteData`
* 使用指定的参数呈现指定组件及其布局（或可选默认布局）。

您可以选择使用布局`DefaultLayout`类指定参数，以便为未指定布局的组件使用。 默认的 Blazor 模板指定`MainLayout`组件。 *MainLayout*位于模板项目的*共享*文件夹中。 有关布局的详细信息，请<xref:blazor/layouts>参阅。

可以将多个路由模板应用于组件。 以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> 若要正确解析 url，应用必须在其*wwwroot/index.html*文件（Blazor 客户端）或*Pages/_Host*文件（Blazor 服务器端）中包含一个`href` `<base>`标记，并在属性中指定应用程序基路径（`<base href="/">`). 有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path> 。

## <a name="provide-custom-content-when-content-isnt-found"></a>当找不到内容时提供自定义内容

如果找不到请求的路由的内容，该组件允许应用指定自定义内容。`Router`

在*app.config*文件中，在`NotFound` `Router`组件的 template 参数中设置自定义内容：

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

`<NotFound>`标记的内容可以包括任意项，如其他交互组件。 若要将默认布局`NotFound`应用于内容，请参阅。 <xref:blazor/layouts>

## <a name="route-to-components-from-multiple-assemblies"></a>从多个程序集中路由到组件

使用参数指定在搜索可路由组件时`Router`要考虑的组件的其他程序集。 `AdditionalAssemblies` 除了指定的程序集`AppAssembly`外，还会考虑指定的程序集。 在下面的示例中`Component1` ，是在引用的类库中定义的可路由组件。 下面`AdditionalAssemblies`的示例将导致路由`Component1`支持：

< 路由器 AppAssembly = "typeof （程序）。程序集 "AdditionalAssemblies =" new [] {typeof （Component1）。Assembly} > .。。</Router>

## <a name="route-parameters"></a>路由参数

路由器使用路由参数来填充具有相同名称（不区分大小写）的相应组件参数：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

ASP.NET Core 3.0 预览中的 Blazor 应用不支持可选参数。 前面`@page`的示例中应用了两个指令。 第一个允许导航到没有参数的组件。 第二`@page`个指令`{text}`采用路由参数，并将值`Text`分配给属性。

## <a name="route-constraints"></a>路由约束

路由约束向组件强制执行对路由段的类型匹配。

在下面的示例中，指向`Users`组件的路由仅在以下情况下才匹配：

* 请求 URL 上存在路由段。`Id`
* 段是整数（`int`）。 `Id`

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

在 Blazor 服务器端应用中， *_Host*中的默认路由是`/` （`@page "/"`）。 由于 url 显示为请求文件，因此`.`包含点（）的请求 URL 不能与默认路由匹配。 对于不存在的静态文件，Blazor 应用返回 " *404-未找到*" 响应。 若要使用包含点的路由，请使用以下路由模板配置 *_Host* ：

```cshtml
@page "/{**path}"
```

该`"/{**path}"`模板包括：

* 双星号*catch-all*语法（`**`）捕获跨多个文件夹边界的路径，而无需编码正`/`斜杠（）。
* `path`路由参数名称。

有关详细信息，请参阅 <xref:fundamentals/routing> 。

## <a name="navlink-component"></a>NavLink 组件

创建导航链接时，请使用`<a>`组件来代替HTMLhyperlink元素（）。`NavLink` 组件`NavLink`的行为`<a>`类似于元素`active` ，只不过它会根据 CSS 类是否`href`与当前 URL 匹配来进行切换。 `active`类可帮助用户了解在显示的导航链接中哪一页是活动页。

以下`NavMenu`组件创建演示如何使用`NavLink`组件的[启动](https://getbootstrap.com/docs/)导航栏：

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

可以将两`NavLinkMatch`个选项分配`Match`给该`<NavLink>`元素的属性：

* `NavLinkMatch.All`当与整个当前 URL 匹配时，处于活动状态。`NavLink` &ndash;
* `NavLinkMatch.Prefix`（*默认值*）&ndash; 当与当前URL的任何前缀匹配时，`NavLink`将处于活动状态。

在前面的示例中，home `NavLink` `href=""`与 home `active` URL 匹配，仅在应用的默认基路径 URL （例如， `https://localhost:5001/`）中接收 CSS 类。 当用户`NavLink`使用`MyComponent`前缀`active` （例如`https://localhost:5001/MyComponent` 和`https://localhost:5001/MyComponent/AnotherSegment`）访问任何 URL 时，第二个会接收该类。

其他`NavLink`组件特性会传递到呈现的定位点标记。 在下面的示例中， `NavLink`组件`target`包含特性：

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

呈现以下 HTML 标记：

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 和导航状态帮助程序

使用`Microsoft.AspNetCore.Components.NavigationManager`在代码中C#处理 uri 和导航。 `NavigationManager`提供下表中显示的事件和方法。

| 成员 | 描述 |
| ------ | ----------- |
| `Uri` | 获取当前的绝对 URI。 |
| `BaseUri` | 获取可附加到相对 URI 路径以生成绝对 URI 的基本 URI （带有尾随斜杠）。 通常， `BaseUri`与*wwwroot/index.html* （ `href` Blazor 客户端）或`<base>` *Pages/_Host* （Blazor 服务器端）中文档的元素的属性相对应。 |
| `NavigateTo` | 定位到指定的 URI。 如果`forceLoad`为`true`：<ul><li>客户端路由被绕过。</li><li>无论 URI 是否通常由客户端路由器处理，浏览器都被迫从服务器加载新页面。</li></ul> |
| `LocationChanged` | 导航位置发生更改时触发的事件。 |
| `ToAbsoluteUri` | 将相对 URI 转换为绝对 URI。 |
| `ToBaseRelativePath` | 给定基 uri （例如，先前由返回的`GetBaseUri`uri），会将绝对 uri 转换为相对于基 uri 前缀的 uri。 |

选择该按钮时，以下组件将`Counter`导航到应用的组件：

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
