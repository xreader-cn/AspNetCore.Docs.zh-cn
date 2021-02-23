---
title: ASP.NET Core Blazor 级联值和参数
author: guardrex
description: 了解如何将数据从祖先组件流向子代组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280233"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor 级联值和参数

级联值和参数提供了一种方便的方法，可将数据沿组件层次结构从祖先组件向下流向任意数量的后代组件。 不同于[组件参数](xref:blazor/components/index#component-parameters)，级联值和参数不需要对使用数据的每个后代组件分配特性。 级联值和参数还允许组件在组件层次结构中相互协调。

## <a name="cascadingvalue-component"></a>`CascadingValue` 组件

祖先组件使用 Blazor 框架的 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件提供级联值，该组件包装组件层次结构的子树，并向其子树中的所有组件提供单个值。

下面的示例演示了主题信息沿布局组件的组件层次结构向下流动，以便为子组件中的按钮提供 CSS 样式的类。

下面的 `ThemeInfo` C# 类放置在名为 `UIThemeClasses` 的文件夹中，并指定主题信息。

> [!NOTE]
> 对于本部分中的示例，应用的命名空间为 `BlazorSample`。 在自己的示例应用中试验代码时，请将应用的命名空间更改为示例应用的命名空间。

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

下面的[布局组件](xref:blazor/layouts)将主题信息 (`ThemeInfo`) 指定为构成 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 属性布局主体的所有组件的级联值。 `ButtonClass` 分配有值 [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/)，这是一种启动按钮样式。 组件层次结构中的任何后代组件都可以通过 `ThemeInfo` 级联值来使用 `ButtonClass` 属性。

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>`[CascadingParameter]` 特性

为了使用级联值，后代组件使用 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)来声明级联参数。 级联值按类型绑定到级联参数。 本文后面的[级联多个值](#cascade-multiple-values)部分中介绍了相同类型的级联多个值。

以下组件将 `ThemeInfo` 级联值绑定到级联参数，并且可以选择使用相同的名称 `ThemeInfo`。 该参数用于设置 `Increment Counter (Themed)` 按钮的 CSS 类。

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>级联多个值

若要在同一子树内级联多个相同类型的值，请向每个 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件及其相应的 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)提供唯一的 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 字符串。

在下面的示例中，两个 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件级联 `CascadingType` 的不同实例：

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在后代组件中，级联参数按 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 从祖先组件中接收其级联值：

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>跨组件层次结构传递数据

级联参数还允许组件跨组件层次结构传递数据。 请考虑下面的 UI 选项卡集示例，其中选项卡集组件维护一系列单独的选项卡。

> [!NOTE]
> 对于本部分中的示例，应用的命名空间为 `BlazorSample`。 在自己的示例应用中试验代码时，请将命名空间更改为示例应用的命名空间。

创建选项卡在名为 `UIInterfaces` 的文件夹中实现的 `ITab` 接口。

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

以下 `TabSet` 组件维护一组选项卡。 选项卡集的 `Tab` 组件（在本部分后面创建）为列表 (`<ul>...</ul>`) 提供列表项 (`<li>...</li>`)。

子 `Tab` 组件不会作为参数显式传递给 `TabSet`。 子 `Tab` 组件是 `TabSet` 的子内容的一部分。 但 `TabSet` 仍需要每个 `Tab` 组件的引用，以便它可以呈现标头和活动选项卡。若要在不需要额外代码的情况下启用此协调，`TabSet` 组件可以将自身作为级联值提供，然后由子代 `Tab` 组件选取。

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

后代 `Tab` 组件将包含的 `TabSet` 作为级联参数捕获。 `Tab` 组件将自己添加到 `TabSet` 和坐标以设置活动选项卡。

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

以下 `ExampleTabSet` 组件使用 `TabSet` 组件，其中包含三个 `Tab` 组件。

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
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
