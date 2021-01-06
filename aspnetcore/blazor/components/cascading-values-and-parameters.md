---
title: ASP.NET Core Blazor 级联值和参数
author: guardrex
description: 了解如何将数据从祖先组件流向子代组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 56d70cea50a3a913b4483f6ea488438269aa58fe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507975"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor 级联值和参数

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

在某些情况下，使用[组件参数](xref:blazor/components/index#component-parameters)将数据从祖先组件流向子代组件不太方便，尤其是在有多个组件层时。 级联值和参数提供了一种方便的方法，使祖先组件为其所有子代组件提供值，从而解决了此问题。 级联值和参数还提供了一种协调组件的方法。

### <a name="theme-example"></a>主题示例

在示例应用的以下示例中，`ThemeInfo` 类指定了要沿组件层次结构向下传递的主题信息，以便应用中给定部分内的所有按钮共享相同样式。

`UIThemeClasses/ThemeInfo.cs`：

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

祖先组件可以使用级联值组件提供级联值。 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。

例如，示例应用将应用布局之一中的主题信息 (`ThemeInfo`) 指定为组成 `@Body` 属性布局正文的所有组件的级联参数。 在布局组件中为 `ButtonClass` 分配了 `btn-success` 的值。 任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。

`CascadingValuesParametersLayout` 组件：

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

为了使用级联值，组件使用 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 特性来声明级联参数。 级联值按类型绑定到级联参数。

在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。 该参数用于为组件显示的按钮之一设置 CSS 类。

`CascadingValuesParametersTheme` 组件：

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

若要在同一子树内级联多个相同类型的值，请向每个 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 组件及其相应的 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 特性提供唯一的 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 字符串。 在下面的示例中，两个 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 组件按名称级联 `MyCascadingType` 的不同实例：

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在子代组件中，级联参数按名称从祖先组件中相应的级联值接收值：

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet 示例

级联参数还允许组件跨组件层次结构进行协作。 例如，请考虑示例应用中的以下 `TabSet` 示例。

该示例应用包含一个选项卡实现的 `ITab` 接口：

[!code-csharp[](../common/samples/5.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` 组件使用 `TabSet` 组件，其中包含多个 `Tab` 组件：

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

子 `Tab` 组件不会作为参数显式传递给 `TabSet`。 子 `Tab` 组件是 `TabSet` 的子内容的一部分。 但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要在不需要额外代码的情况下启用此协调，`TabSet` 组件可以将自身作为级联值提供，然后由子代 `Tab` 组件选取。

`TabSet` 组件：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子代 `Tab` 组件将包含的 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并在处于活动状态的选项卡上进行协调。

`Tab` 组件：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/Tab.razor)]
