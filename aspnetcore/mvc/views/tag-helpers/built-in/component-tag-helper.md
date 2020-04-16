---
title: ASP.NET核心中的组件标记帮助程序
author: guardrex
ms.author: riande
description: 了解如何使用ASP.NET核心组件标记帮助器在页面和视图中呈现 Razor 组件。
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440956"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET核心中的组件标记帮助程序

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)。

## <a name="prerequisites"></a>先决条件

按照"准备应用"中的指南在<xref:blazor/integrate-components#prepare-the-app>文章的*页面和视图部分使用组件*。

## <a name="component-tag-helper"></a>组件标记帮助程序

以下组件标记帮助程序在页面或视图中`Counter`呈现组件：

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

前面的示例假定组件`Counter`位于应用的 *"主页"* 文件夹中。

组件标记帮助程序还可以将参数传递给组件。 请考虑设置复选框`ColorfulCheckbox`标签颜色和大小的以下组件：

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

`Size` （`int`）`Color`和`string`（ ）[组件参数](xref:blazor/components#component-parameters)可以由组件标记帮助程序设置：

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

前面的示例假定组件`ColorfulCheckbox`位于应用的 *"共享"* 文件夹中。

以下 HTML 呈现在页面或视图中：

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

传递引用的字符串需要[显式 Razor 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如前面的`param-Color`示例所示。 `string`类型值的 Razor 解析行为不适用于属性，`param-*`因为属性是一种`object`类型。

参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置属性。 例如，`Size`您可以为 和 在前面的示例中`Color`指定值，因为 和`Size``Color`的类型是基元类型`int`（`string`和 ），JSON 序列化器支持。

在下面的示例中，类对象传递给组件：

*MyClass.cs*：

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**类必须具有公共无参数构造函数。**

*共享/我的组件.razor*：

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*页面/MyPage.cshtml*：

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

前面的示例假定组件`MyComponent`位于应用的 *"共享"* 文件夹中。 `MyClass`在应用的命名空间中 。`{APP ASSEMBLY}`

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>配置组件是否：

* 预置到页面中。
* 在页面上呈现为静态 HTML，或者如果它包含从用户代理引导 Blazor 应用的必要信息。

| 渲染模式 | 说明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 将组件呈现为静态 HTML，并包含服务器应用的Blazor标记。 当用户代理启动时，此标记用于引导Blazor应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 渲染服务器应用的Blazor标记。 不包括组件的输出。 当用户代理启动时，此标记用于引导Blazor应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 将组件呈现为静态 HTML。 |

虽然页面和视图可以使用组件，但事实并非如此。 组件无法使用特定于视图和页面的功能，如部分视图和节。 要使用组件中部分视图的逻辑，将部分视图逻辑分解到组件中。

不支持从静态 HTML 页呈现服务器组件。

## <a name="additional-resources"></a>其他资源

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
