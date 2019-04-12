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
# <a name="razor-components-routing"></a>路由的 razor 组件

作者：[Luke Latham](https://github.com/guardrex)

了解如何将请求路由在应用和有关 NavLink 组件。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 终结点路由集成

Razor 组件集成到[ASP.NET Core 路由](xref:fundamentals/routing)。 ASP.NET Core 应用配置为接受传入连接的具有交互式 Razor 组件`MapComponentHub<TComponent>`在`Startup.Configure`。 `MapComponentHub` 指定的根组件`App`应匹配选择器的 DOM 元素中呈现`app`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a>路由模板

`<Router>`组件允许路由和路由模板提供给每个可访问的组件。 `<Router>`部分显示在*Components/App.razor*文件：

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

当 *.razor*或 *.cshtml*文件`@page`编译指令，则生成的类提供<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>指定路由模板。 在运行时，路由器将查看与组件类`RouteAttribute`并呈现任何组件有匹配的请求的 URL 的路由模板。

多个路由模板可以应用于一个组件。 以下组件响应的请求`/BlazorRoute`和`/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` 支持设置呈现时请求的路由的回退组件进行解析。 通过设置启用此选择加入方案`FallbackComponent`回退组件类的类型参数。

下面的示例设置中定义的组件*Pages/MyFallbackRazorComponent.cshtml*是回退组件`<Router>`:

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> 若要正确生成的路由，该应用程序必须包括`<base>`标记中的其*wwwroot/index.html*文件中指定的应用程序基路径`href`属性 (`<base href="/">`)。 有关详细信息，请参阅 <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>。

## <a name="route-parameters"></a>路由参数

路由器使用路由参数来填充相应的组件参数具有相同名称 （不区分大小写）：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

可选参数不受支持，因此两个`@page`指令收取在上面的示例。 第一个可以导航到不带参数的组件。 第二个`@page`指令采用`{text}`路由参数和值赋给`Text`属性。

## <a name="route-constraints"></a>路由约束

路由约束强制实施类型到一个组件路由段进行匹配。

在以下示例中，如果只与匹配的路由到用户组件：

* `Id`路由段是存在于请求 URL 上。
* `Id`段是一个整数 (`int`)。

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

提供下表中所示的路由约束。 使用固定区域性匹配的路由约束，请参阅下表了解详细信息的警告。

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
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。 这些约束假定 URL 不可本地化。

## <a name="navlink-component"></a>NavLink 组件

使用取代 HTML NavLink 组件`<a>`元素创建导航链接时。 NavLink 组件的行为类似于`<a>`元素，但它切换`active`CSS 类是否基于其`href`当前 URL 匹配。 `active`类可帮助用户了解哪页不显示导航链接中的活动页面。

以下 NavMenu 组件创建[Bootstrap](https://getbootstrap.com/docs/)演示了如何使用 NavLink 组件的导航栏：

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

有两个`NavLinkMatch`选项：

* `NavLinkMatch.All` &ndash; 指定，它与匹配整个当前 URL 时，NavLink 应处于活动状态。
* `NavLinkMatch.Prefix` &ndash; 指定，它与匹配的任何前缀的当前 URL 时，NavLink 应处于活动状态。

在前面的示例中，主页 NavLink (`href=""`) 匹配的所有 Url 并始终接收`active`CSS 类。 仅接收第二个 NavLink`active`类在用户访问 Blazor 路由组件 (`href="BlazorRoute"`)。
