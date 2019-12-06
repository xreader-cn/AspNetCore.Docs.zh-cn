---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 280ea832f492852e425e3e15c61cac54fd1e39d6
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879675"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor 生命周期

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

Blazor 框架包括同步和异步生命周期方法。 重写生命周期方法，以便在组件初始化和呈现期间对组件执行其他操作。

## <a name="lifecycle-methods"></a>生命周期方法

### <a name="component-initialization-methods"></a>组件初始化方法

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> 执行用于初始化组件的代码。 仅在第一次实例化组件时，才会调用这些方法一次。

若要执行异步操作，请在操作中使用 `OnInitializedAsync` 和 `await` 关键字：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> 在 `OnInitializedAsync` 生命周期事件期间，必须在组件初始化期间进行异步工作。

对于同步操作，请使用 `OnInitialized`：

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a>设置参数之前

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 设置由呈现树中的组件父级提供的参数：

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> 包含每次调用 `SetParametersAsync` 时的完整参数值集。

`SetParametersAsync` 的默认实现将每个属性的值设置为在 `ParameterView`中具有相应值的 `[Parameter]` 或 `[CascadingParameter]` 特性。 在 `ParameterView` 中没有相应值的参数将保持不变。

如果未调用 `base.SetParametersAync`，则自定义代码可以通过任何所需的方式解释传入参数值。 例如，不要求将传入参数分配给类的属性。

### <a name="after-parameters-are-set"></a>设置参数后

当组件已从其父级接收参数并且为属性分配了值时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>。 这些方法在组件初始化之后以及每次指定新参数值时执行：

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>组件呈现后

当组件完成呈现后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>。 此时将填充元素和组件引用。 使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。

`OnAfterRenderAsync` 和 `OnAfterRender`的 `firstRender` 参数：

* 在第一次呈现组件实例时，设置为 `true`。
* 可用于确保仅执行一次初始化工作。

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> 在 `OnAfterRenderAsync` 生命周期事件期间，必须立即进行渲染后的异步工作。
>
> 即使从 `OnAfterRenderAsync`中返回 <xref:System.Threading.Tasks.Task>，该任务完成后，框架也不会为组件计划进一步的呈现循环。 这是为了避免出现无限的呈现循环。 它与其他生命周期方法不同，后者将在返回的任务完成后计划更多的渲染循环。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

在*服务器上预呈现时不会调用*`OnAfterRender` 和 `OnAfterRenderAsync`。

### <a name="suppress-ui-refreshing"></a>禁止 UI 刷新

重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以取消 UI 刷新。 如果实现返回 `true`，则将刷新 UI：

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

每次呈现组件时，都将调用 `ShouldRender`。

即使 `ShouldRender` 被重写，组件也始终是最初呈现的。

## <a name="state-changes"></a>状态更改

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件状态已更改。 如果适用，调用 `StateHasChanged` 会导致组件重新呈现。

## <a name="handle-incomplete-async-actions-at-render"></a>在呈现时处理未完成的异步操作

在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 在执行生命周期方法时，对象可能 `null` 或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 `null`对象时呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。 `null``forecasts` 时，将向用户显示一条加载消息。 `OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。

*页面/FetchData* Blazor 服务器模板：

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>用 IDisposable 进行的组件处置

如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。 以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> 不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。 `StateHasChanged` 可以作为撕裂渲染器的一部分被调用，因此不支持在该点请求 UI 更新。

## <a name="handle-errors"></a>处理错误

有关在执行生命周期方法期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。
