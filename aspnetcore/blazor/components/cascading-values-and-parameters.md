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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="916ff-103">ASP.NET Core Blazor 级联值和参数</span><span class="sxs-lookup"><span data-stu-id="916ff-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="916ff-104">级联值和参数提供了一种方便的方法，可将数据沿组件层次结构从祖先组件向下流向任意数量的后代组件。</span><span class="sxs-lookup"><span data-stu-id="916ff-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="916ff-105">不同于[组件参数](xref:blazor/components/index#component-parameters)，级联值和参数不需要对使用数据的每个后代组件分配特性。</span><span class="sxs-lookup"><span data-stu-id="916ff-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="916ff-106">级联值和参数还允许组件在组件层次结构中相互协调。</span><span class="sxs-lookup"><span data-stu-id="916ff-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="916ff-107">`CascadingValue` 组件</span><span class="sxs-lookup"><span data-stu-id="916ff-107">`CascadingValue` component</span></span>

<span data-ttu-id="916ff-108">祖先组件使用 Blazor 框架的 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件提供级联值，该组件包装组件层次结构的子树，并向其子树中的所有组件提供单个值。</span><span class="sxs-lookup"><span data-stu-id="916ff-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="916ff-109">下面的示例演示了主题信息沿布局组件的组件层次结构向下流动，以便为子组件中的按钮提供 CSS 样式的类。</span><span class="sxs-lookup"><span data-stu-id="916ff-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="916ff-110">下面的 `ThemeInfo` C# 类放置在名为 `UIThemeClasses` 的文件夹中，并指定主题信息。</span><span class="sxs-lookup"><span data-stu-id="916ff-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="916ff-111">对于本部分中的示例，应用的命名空间为 `BlazorSample`。</span><span class="sxs-lookup"><span data-stu-id="916ff-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="916ff-112">在自己的示例应用中试验代码时，请将应用的命名空间更改为示例应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="916ff-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="916ff-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="916ff-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="916ff-114">下面的[布局组件](xref:blazor/layouts)将主题信息 (`ThemeInfo`) 指定为构成 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 属性布局主体的所有组件的级联值。</span><span class="sxs-lookup"><span data-stu-id="916ff-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="916ff-115">`ButtonClass` 分配有值 [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/)，这是一种启动按钮样式。</span><span class="sxs-lookup"><span data-stu-id="916ff-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="916ff-116">组件层次结构中的任何后代组件都可以通过 `ThemeInfo` 级联值来使用 `ButtonClass` 属性。</span><span class="sxs-lookup"><span data-stu-id="916ff-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="916ff-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="916ff-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="916ff-118">`[CascadingParameter]` 特性</span><span class="sxs-lookup"><span data-stu-id="916ff-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="916ff-119">为了使用级联值，后代组件使用 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)来声明级联参数。</span><span class="sxs-lookup"><span data-stu-id="916ff-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="916ff-120">级联值按类型绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="916ff-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="916ff-121">本文后面的[级联多个值](#cascade-multiple-values)部分中介绍了相同类型的级联多个值。</span><span class="sxs-lookup"><span data-stu-id="916ff-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="916ff-122">以下组件将 `ThemeInfo` 级联值绑定到级联参数，并且可以选择使用相同的名称 `ThemeInfo`。</span><span class="sxs-lookup"><span data-stu-id="916ff-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="916ff-123">该参数用于设置 `Increment Counter (Themed)` 按钮的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="916ff-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="916ff-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="916ff-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="916ff-125">级联多个值</span><span class="sxs-lookup"><span data-stu-id="916ff-125">Cascade multiple values</span></span>

<span data-ttu-id="916ff-126">若要在同一子树内级联多个相同类型的值，请向每个 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件及其相应的 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)提供唯一的 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 字符串。</span><span class="sxs-lookup"><span data-stu-id="916ff-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="916ff-127">在下面的示例中，两个 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 组件级联 `CascadingType` 的不同实例：</span><span class="sxs-lookup"><span data-stu-id="916ff-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

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

<span data-ttu-id="916ff-128">在后代组件中，级联参数按 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 从祖先组件中接收其级联值：</span><span class="sxs-lookup"><span data-stu-id="916ff-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="916ff-129">跨组件层次结构传递数据</span><span class="sxs-lookup"><span data-stu-id="916ff-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="916ff-130">级联参数还允许组件跨组件层次结构传递数据。</span><span class="sxs-lookup"><span data-stu-id="916ff-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="916ff-131">请考虑下面的 UI 选项卡集示例，其中选项卡集组件维护一系列单独的选项卡。</span><span class="sxs-lookup"><span data-stu-id="916ff-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="916ff-132">对于本部分中的示例，应用的命名空间为 `BlazorSample`。</span><span class="sxs-lookup"><span data-stu-id="916ff-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="916ff-133">在自己的示例应用中试验代码时，请将命名空间更改为示例应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="916ff-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="916ff-134">创建选项卡在名为 `UIInterfaces` 的文件夹中实现的 `ITab` 接口。</span><span class="sxs-lookup"><span data-stu-id="916ff-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="916ff-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="916ff-135">`UIInterfaces/ITab.cs`:</span></span>

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

<span data-ttu-id="916ff-136">以下 `TabSet` 组件维护一组选项卡。</span><span class="sxs-lookup"><span data-stu-id="916ff-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="916ff-137">选项卡集的 `Tab` 组件（在本部分后面创建）为列表 (`<ul>...</ul>`) 提供列表项 (`<li>...</li>`)。</span><span class="sxs-lookup"><span data-stu-id="916ff-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="916ff-138">子 `Tab` 组件不会作为参数显式传递给 `TabSet`。</span><span class="sxs-lookup"><span data-stu-id="916ff-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="916ff-139">子 `Tab` 组件是 `TabSet` 的子内容的一部分。</span><span class="sxs-lookup"><span data-stu-id="916ff-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="916ff-140">但 `TabSet` 仍需要每个 `Tab` 组件的引用，以便它可以呈现标头和活动选项卡。若要在不需要额外代码的情况下启用此协调，`TabSet` 组件可以将自身作为级联值提供，然后由子代 `Tab` 组件选取。</span><span class="sxs-lookup"><span data-stu-id="916ff-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="916ff-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="916ff-141">`Shared/TabSet.razor`:</span></span>

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

<span data-ttu-id="916ff-142">后代 `Tab` 组件将包含的 `TabSet` 作为级联参数捕获。</span><span class="sxs-lookup"><span data-stu-id="916ff-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="916ff-143">`Tab` 组件将自己添加到 `TabSet` 和坐标以设置活动选项卡。</span><span class="sxs-lookup"><span data-stu-id="916ff-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="916ff-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="916ff-144">`Shared/Tab.razor`:</span></span>

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

<span data-ttu-id="916ff-145">以下 `ExampleTabSet` 组件使用 `TabSet` 组件，其中包含三个 `Tab` 组件。</span><span class="sxs-lookup"><span data-stu-id="916ff-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="916ff-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="916ff-146">`Pages/ExampleTabSet.razor`:</span></span>

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
