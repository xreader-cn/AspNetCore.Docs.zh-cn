---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 1482f6b2147c74b11836e8029401bb8bcb3cdb2d
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681373"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="57922-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="57922-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="57922-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="57922-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="57922-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="57922-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="57922-106">重写生命周期方法，以便在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="57922-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="57922-107">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="57922-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="57922-108">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="57922-108">Component initialization methods</span></span>

<span data-ttu-id="57922-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> 执行用于初始化组件的代码。</span><span class="sxs-lookup"><span data-stu-id="57922-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="57922-110">仅在第一次实例化组件时，才会调用这些方法一次。</span><span class="sxs-lookup"><span data-stu-id="57922-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="57922-111">若要执行异步操作，请在操作中使用 `OnInitializedAsync` 和 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="57922-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="57922-112">在 `OnInitializedAsync` 生命周期事件期间，必须在组件初始化期间进行异步工作。</span><span class="sxs-lookup"><span data-stu-id="57922-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="57922-113">对于同步操作，请使用 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="57922-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="57922-114">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="57922-114">Before parameters are set</span></span>

<span data-ttu-id="57922-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 设置由呈现树中的组件父级提供的参数：</span><span class="sxs-lookup"><span data-stu-id="57922-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="57922-116"><xref:Microsoft.AspNetCore.Components.ParameterView> 包含每次调用 `SetParametersAsync` 时的完整参数值集。</span><span class="sxs-lookup"><span data-stu-id="57922-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="57922-117">`SetParametersAsync` 的默认实现设置使用 `[Parameter]` 或 `[CascadingParameter]` 特性修饰的每个属性的值，该属性在 `ParameterView`中具有相应的值。</span><span class="sxs-lookup"><span data-stu-id="57922-117">The default implementation of `SetParametersAsync` sets the value of each property decorated with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="57922-118">在 `ParameterView` 中没有相应值的参数将保持不变。</span><span class="sxs-lookup"><span data-stu-id="57922-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="57922-119">如果未调用 `base.SetParametersAync`，则自定义代码可以通过任何所需的方式解释传入参数值。</span><span class="sxs-lookup"><span data-stu-id="57922-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="57922-120">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="57922-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="57922-121">设置参数后</span><span class="sxs-lookup"><span data-stu-id="57922-121">After parameters are set</span></span>

<span data-ttu-id="57922-122">当组件已从其父级接收参数并且为属性分配了值时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>。</span><span class="sxs-lookup"><span data-stu-id="57922-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="57922-123">这些方法在组件初始化之后以及每次指定新参数值时执行：</span><span class="sxs-lookup"><span data-stu-id="57922-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="57922-124">应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="57922-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="57922-125">组件呈现后</span><span class="sxs-lookup"><span data-stu-id="57922-125">After component render</span></span>

<span data-ttu-id="57922-126">当组件完成呈现后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>。</span><span class="sxs-lookup"><span data-stu-id="57922-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="57922-127">此时将填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="57922-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="57922-128">使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="57922-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="57922-129">`OnAfterRenderAsync` 和 `OnAfterRender`的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="57922-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="57922-130">在第一次呈现组件实例时，设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="57922-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="57922-131">可用于确保仅执行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="57922-131">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="57922-132">在 `OnAfterRenderAsync` 生命周期事件期间，必须立即进行渲染后的异步工作。</span><span class="sxs-lookup"><span data-stu-id="57922-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="57922-133">即使从 `OnAfterRenderAsync`中返回 <xref:System.Threading.Tasks.Task>，该任务完成后，框架也不会为组件计划进一步的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="57922-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="57922-134">这是为了避免出现无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="57922-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="57922-135">它与其他生命周期方法不同，后者将在返回的任务完成后计划更多的渲染循环。</span><span class="sxs-lookup"><span data-stu-id="57922-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="57922-136">在*服务器上预呈现时不会调用*`OnAfterRender` 和 `OnAfterRenderAsync`。</span><span class="sxs-lookup"><span data-stu-id="57922-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="57922-137">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="57922-137">Suppress UI refreshing</span></span>

<span data-ttu-id="57922-138">重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以取消 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="57922-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="57922-139">如果实现返回 `true`，则将刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="57922-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="57922-140">每次呈现组件时，都将调用 `ShouldRender`。</span><span class="sxs-lookup"><span data-stu-id="57922-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="57922-141">即使 `ShouldRender` 被重写，组件也始终是最初呈现的。</span><span class="sxs-lookup"><span data-stu-id="57922-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="57922-142">状态更改</span><span class="sxs-lookup"><span data-stu-id="57922-142">State changes</span></span>

<span data-ttu-id="57922-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件状态已更改。</span><span class="sxs-lookup"><span data-stu-id="57922-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="57922-144">如果适用，调用 `StateHasChanged` 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="57922-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="57922-145">在呈现时处理未完成的异步操作</span><span class="sxs-lookup"><span data-stu-id="57922-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="57922-146">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="57922-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="57922-147">在执行生命周期方法时，对象可能 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="57922-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="57922-148">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="57922-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="57922-149">`null`对象时呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="57922-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="57922-150">在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。</span><span class="sxs-lookup"><span data-stu-id="57922-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="57922-151">`null``forecasts` 时，将向用户显示一条加载消息。</span><span class="sxs-lookup"><span data-stu-id="57922-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="57922-152">`OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。</span><span class="sxs-lookup"><span data-stu-id="57922-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="57922-153">*页面/FetchData* Blazor 服务器模板：</span><span class="sxs-lookup"><span data-stu-id="57922-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="57922-154">用 IDisposable 进行的组件处置</span><span class="sxs-lookup"><span data-stu-id="57922-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="57922-155">如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="57922-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="57922-156">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="57922-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="57922-157">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。</span><span class="sxs-lookup"><span data-stu-id="57922-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="57922-158">`StateHasChanged` 可以作为撕裂渲染器的一部分被调用，因此不支持在该点请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="57922-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="57922-159">处理错误</span><span class="sxs-lookup"><span data-stu-id="57922-159">Handle errors</span></span>

<span data-ttu-id="57922-160">有关在执行生命周期方法期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="57922-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
