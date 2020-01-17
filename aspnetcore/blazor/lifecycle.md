---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146363"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="0e82a-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="0e82a-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="0e82a-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0e82a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0e82a-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="0e82a-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="0e82a-106">重写生命周期方法，以便在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="0e82a-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="0e82a-107">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="0e82a-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="0e82a-108">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="0e82a-108">Component initialization methods</span></span>

<span data-ttu-id="0e82a-109">当组件从其父组件收到其初始参数后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>。</span><span class="sxs-lookup"><span data-stu-id="0e82a-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="0e82a-110">当组件执行异步操作时，请使用 `OnInitializedAsync`，并在操作完成时进行刷新。</span><span class="sxs-lookup"><span data-stu-id="0e82a-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span> <span data-ttu-id="0e82a-111">仅在第一次实例化组件时，才会调用这些方法一次。</span><span class="sxs-lookup"><span data-stu-id="0e82a-111">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="0e82a-112">对于同步操作，请重写 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="0e82a-112">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="0e82a-113">若要执行异步操作，请重写 `OnInitializedAsync`，并对操作使用 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="0e82a-113">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="0e82a-114">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="0e82a-114">Before parameters are set</span></span>

<span data-ttu-id="0e82a-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 设置由呈现树中的组件父级提供的参数：</span><span class="sxs-lookup"><span data-stu-id="0e82a-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="0e82a-116"><xref:Microsoft.AspNetCore.Components.ParameterView> 包含每次调用 `SetParametersAsync` 时的完整参数值集。</span><span class="sxs-lookup"><span data-stu-id="0e82a-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="0e82a-117">`SetParametersAsync` 的默认实现将每个属性的值设置为在 `ParameterView`中具有相应值的 `[Parameter]` 或 `[CascadingParameter]` 特性。</span><span class="sxs-lookup"><span data-stu-id="0e82a-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="0e82a-118">在 `ParameterView` 中没有相应值的参数将保持不变。</span><span class="sxs-lookup"><span data-stu-id="0e82a-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="0e82a-119">如果未调用 `base.SetParametersAync`，则自定义代码可以通过任何所需的方式解释传入参数值。</span><span class="sxs-lookup"><span data-stu-id="0e82a-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="0e82a-120">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="0e82a-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="0e82a-121">设置参数后</span><span class="sxs-lookup"><span data-stu-id="0e82a-121">After parameters are set</span></span>

<span data-ttu-id="0e82a-122">调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>：</span><span class="sxs-lookup"><span data-stu-id="0e82a-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="0e82a-123">当组件已初始化并收到其父组件的第一组参数时。</span><span class="sxs-lookup"><span data-stu-id="0e82a-123">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="0e82a-124">当父组件重新呈现并提供时：</span><span class="sxs-lookup"><span data-stu-id="0e82a-124">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="0e82a-125">仅有至少一个参数发生更改的已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="0e82a-125">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="0e82a-126">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="0e82a-126">Any complex-typed parameters.</span></span> <span data-ttu-id="0e82a-127">此框架无法知道复杂类型参数的值是否在内部转变，因此它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="0e82a-127">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="0e82a-128">应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="0e82a-128">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="0e82a-129">组件呈现后</span><span class="sxs-lookup"><span data-stu-id="0e82a-129">After component render</span></span>

<span data-ttu-id="0e82a-130">当组件完成呈现后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>。</span><span class="sxs-lookup"><span data-stu-id="0e82a-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="0e82a-131">此时将填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="0e82a-131">Element and component references are populated at this point.</span></span> <span data-ttu-id="0e82a-132">使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="0e82a-132">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="0e82a-133">`OnAfterRenderAsync` 和 `OnAfterRender`的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="0e82a-133">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="0e82a-134">在第一次呈现组件实例时，设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="0e82a-134">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="0e82a-135">可用于确保仅执行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="0e82a-135">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="0e82a-136">在 `OnAfterRenderAsync` 生命周期事件期间，必须立即进行渲染后的异步工作。</span><span class="sxs-lookup"><span data-stu-id="0e82a-136">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="0e82a-137">即使从 `OnAfterRenderAsync`中返回 <xref:System.Threading.Tasks.Task>，该任务完成后，框架也不会为组件计划进一步的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="0e82a-137">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="0e82a-138">这是为了避免出现无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="0e82a-138">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="0e82a-139">它与其他生命周期方法不同，后者将在返回的任务完成后计划更多的渲染循环。</span><span class="sxs-lookup"><span data-stu-id="0e82a-139">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="0e82a-140">在*服务器上预呈现时不会调用*`OnAfterRender` 和 `OnAfterRenderAsync`。</span><span class="sxs-lookup"><span data-stu-id="0e82a-140">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="0e82a-141">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="0e82a-141">Suppress UI refreshing</span></span>

<span data-ttu-id="0e82a-142">重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以取消 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="0e82a-142">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="0e82a-143">如果实现返回 `true`，则将刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="0e82a-143">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="0e82a-144">每次呈现组件时，都将调用 `ShouldRender`。</span><span class="sxs-lookup"><span data-stu-id="0e82a-144">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="0e82a-145">即使 `ShouldRender` 被重写，组件也始终是最初呈现的。</span><span class="sxs-lookup"><span data-stu-id="0e82a-145">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="0e82a-146">状态更改</span><span class="sxs-lookup"><span data-stu-id="0e82a-146">State changes</span></span>

<span data-ttu-id="0e82a-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件状态已更改。</span><span class="sxs-lookup"><span data-stu-id="0e82a-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="0e82a-148">如果适用，调用 `StateHasChanged` 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="0e82a-148">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="0e82a-149">在呈现时处理未完成的异步操作</span><span class="sxs-lookup"><span data-stu-id="0e82a-149">Handle incomplete async actions at render</span></span>

<span data-ttu-id="0e82a-150">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="0e82a-150">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="0e82a-151">在执行生命周期方法时，对象可能 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="0e82a-151">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="0e82a-152">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="0e82a-152">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="0e82a-153">`null`对象时呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="0e82a-153">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="0e82a-154">在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。</span><span class="sxs-lookup"><span data-stu-id="0e82a-154">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="0e82a-155">`null``forecasts` 时，将向用户显示一条加载消息。</span><span class="sxs-lookup"><span data-stu-id="0e82a-155">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="0e82a-156">`OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。</span><span class="sxs-lookup"><span data-stu-id="0e82a-156">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="0e82a-157">*页面/FetchData* Blazor 服务器模板：</span><span class="sxs-lookup"><span data-stu-id="0e82a-157">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="0e82a-158">用 IDisposable 进行的组件处置</span><span class="sxs-lookup"><span data-stu-id="0e82a-158">Component disposal with IDisposable</span></span>

<span data-ttu-id="0e82a-159">如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="0e82a-159">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="0e82a-160">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="0e82a-160">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
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
> <span data-ttu-id="0e82a-161">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。</span><span class="sxs-lookup"><span data-stu-id="0e82a-161">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="0e82a-162">`StateHasChanged` 可以作为撕裂渲染器的一部分被调用，因此不支持在该点请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="0e82a-162">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="0e82a-163">处理错误</span><span class="sxs-lookup"><span data-stu-id="0e82a-163">Handle errors</span></span>

<span data-ttu-id="0e82a-164">有关在执行生命周期方法期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="0e82a-164">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
