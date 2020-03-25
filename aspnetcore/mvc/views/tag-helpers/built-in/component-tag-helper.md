---
title: ASP.NET Core 中的组件标记帮助程序
author: guardrex
ms.author: riande
description: 了解如何使用 ASP.NET Core 组件标记帮助程序在页面和视图中呈现 Razor 组件。
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226380"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的组件标记帮助程序

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

若要从页面或视图呈现组件，请使用[组件标记帮助器](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)。

以下组件标记帮助器呈现页面或视图中的 `Counter` 组件：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

组件标记帮助器还可以将参数传递给组件。 请考虑以下 `ColorfulCheckbox` 组件，该组件设置复选框标签的颜色和大小：

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

`Size` （`int`）和 `Color` （`string`）[组件参数](xref:blazor/components#component-parameters)可由组件标记帮助器设置：

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

在页面或视图中呈现以下 HTML：

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

传递带引号的字符串需要[显式 Razor 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如前面的示例中 `param-Color` 所示。 `string` 类型值的 Razor 分析行为不适用于 `param-*` 属性，因为该属性是 `object` 类型。

参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置的属性。 例如，你可以在前面的示例中为 `Size` 和 `Color` 指定一个值，因为 `Size` 和 `Color` 的类型是 JSON 序列化程序支持的基元类型（`int` 和 `string`）。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：

* 在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

| 呈现模式 | 说明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor 服务器应用的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 将组件呈现为静态 HTML。 |

尽管页面和视图可以使用组件，但反过来则不行。 组件不能使用视图和页特定的功能，如分部视图和节。 若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。

不支持从静态 HTML 页面呈现服务器组件。

## <a name="additional-resources"></a>其他资源

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
