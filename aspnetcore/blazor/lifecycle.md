---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438910"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="273fc-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="273fc-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="273fc-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="273fc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="273fc-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="273fc-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="273fc-106">替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="273fc-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="273fc-107">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="273fc-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="273fc-108">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="273fc-108">Component initialization methods</span></span>

<span data-ttu-id="273fc-109">组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>。</span><span class="sxs-lookup"><span data-stu-id="273fc-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="273fc-110">在组件执行异步操作时使用 `OnInitializedAsync`，并应在操作完成后刷新。</span><span class="sxs-lookup"><span data-stu-id="273fc-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="273fc-111">对于同步操作，替代 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="273fc-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="273fc-112">若要执行异步操作，请替代 `OnInitializedAsync` 并对该操作使用 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="273fc-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="273fc-113">[预呈现其内容](xref:blazor/hosting-model-configuration#render-mode)的 Blazor Server 应用调用 `OnInitializedAsync` **_两次_**：</span><span class="sxs-lookup"><span data-stu-id="273fc-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="273fc-114">在组件最初作为页面的一部分静态呈现时调用一次。</span><span class="sxs-lookup"><span data-stu-id="273fc-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="273fc-115">在浏览器重新建立与服务器的连接时调用第二次。</span><span class="sxs-lookup"><span data-stu-id="273fc-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="273fc-116">为了防止 `OnInitializedAsync` 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="273fc-117">在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行特定操作（例如调用 JavaScript）。</span><span class="sxs-lookup"><span data-stu-id="273fc-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="273fc-118">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="273fc-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="273fc-119">有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="273fc-120">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="273fc-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="273fc-121">有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="273fc-122">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="273fc-122">Before parameters are set</span></span>

<span data-ttu-id="273fc-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在呈现树中设置组件的父组件提供的参数：</span><span class="sxs-lookup"><span data-stu-id="273fc-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="273fc-124">每次调用 `SetParametersAsync` 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含整个参数值集。</span><span class="sxs-lookup"><span data-stu-id="273fc-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="273fc-125">`SetParametersAsync` 的默认实现使用 `[Parameter]` 或 `[CascadingParameter]` 特性（在 `ParameterView` 中具有对应的值）设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="273fc-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="273fc-126">在 `ParameterView` 中没有对应值的参数保持不变。</span><span class="sxs-lookup"><span data-stu-id="273fc-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="273fc-127">如果未调用 `base.SetParametersAync`，则自定义代码可使用任何需要的方式解释传入的参数值。</span><span class="sxs-lookup"><span data-stu-id="273fc-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="273fc-128">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="273fc-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="273fc-129">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="273fc-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="273fc-130">有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="273fc-131">设置参数之后</span><span class="sxs-lookup"><span data-stu-id="273fc-131">After parameters are set</span></span>

<span data-ttu-id="273fc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 在以下情况下调用：</span><span class="sxs-lookup"><span data-stu-id="273fc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="273fc-133">当组件被初始化并从其父组件收到其第一组参数时。</span><span class="sxs-lookup"><span data-stu-id="273fc-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="273fc-134">当父组件重新呈现并提供以下内容时：</span><span class="sxs-lookup"><span data-stu-id="273fc-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="273fc-135">至少一个参数已更改的唯一已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="273fc-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="273fc-136">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="273fc-136">Any complex-typed parameters.</span></span> <span data-ttu-id="273fc-137">框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="273fc-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="273fc-138">应用参数和属性值时，异步操作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="273fc-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="273fc-139">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="273fc-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="273fc-140">有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="273fc-141">组件呈现之后</span><span class="sxs-lookup"><span data-stu-id="273fc-141">After component render</span></span>

<span data-ttu-id="273fc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="273fc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="273fc-143">此时会填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="273fc-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="273fc-144">在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="273fc-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="273fc-145">`OnAfterRenderAsync` 和 `OnAfterRender` 的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="273fc-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="273fc-146">在第一次呈现组件实例时设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="273fc-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="273fc-147">可用于确保初始化操作仅执行一次。</span><span class="sxs-lookup"><span data-stu-id="273fc-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="273fc-148">呈现后立即进行的异步操作必须在 `OnAfterRenderAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="273fc-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="273fc-149">即使从 `OnAfterRenderAsync` 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。</span><span class="sxs-lookup"><span data-stu-id="273fc-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="273fc-150">这是为了避免无限呈现循环。</span><span class="sxs-lookup"><span data-stu-id="273fc-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="273fc-151">它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。</span><span class="sxs-lookup"><span data-stu-id="273fc-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="273fc-152">*在服务器上进行预呈现时*未调用 `OnAfterRender` 和 `OnAfterRenderAsync`。</span><span class="sxs-lookup"><span data-stu-id="273fc-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="273fc-153">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="273fc-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="273fc-154">有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="273fc-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="273fc-155">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="273fc-155">Suppress UI refreshing</span></span>

<span data-ttu-id="273fc-156">替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以禁止 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="273fc-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="273fc-157">如果实现返回 `true`，则刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="273fc-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="273fc-158">每次呈现组件时都会调用 `ShouldRender`。</span><span class="sxs-lookup"><span data-stu-id="273fc-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="273fc-159">即使 `ShouldRender` 被替代，组件也始终在最初呈现。</span><span class="sxs-lookup"><span data-stu-id="273fc-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="273fc-160">有关详细信息，请参阅 <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>。</span><span class="sxs-lookup"><span data-stu-id="273fc-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="273fc-161">状态更改</span><span class="sxs-lookup"><span data-stu-id="273fc-161">State changes</span></span>

<span data-ttu-id="273fc-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。</span><span class="sxs-lookup"><span data-stu-id="273fc-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="273fc-163">如果适用，调用 `StateHasChanged` 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="273fc-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="273fc-164">处理呈现时的不完整异步操作</span><span class="sxs-lookup"><span data-stu-id="273fc-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="273fc-165">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="273fc-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="273fc-166">执行生命周期方法时，对象可能为 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="273fc-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="273fc-167">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="273fc-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="273fc-168">对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="273fc-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="273fc-169">在 Blazor 模板的 `FetchData` 组件中，替代 `OnInitializedAsync` 以异步接收预测数据 (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="273fc-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="273fc-170">当 `forecasts` 为 `null` 时，将向用户显示加载消息。</span><span class="sxs-lookup"><span data-stu-id="273fc-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="273fc-171">`OnInitializedAsync` 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。</span><span class="sxs-lookup"><span data-stu-id="273fc-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="273fc-172">Blazor Server 模板中的 *Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="273fc-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="273fc-173">使用 IDisposable 处置组件</span><span class="sxs-lookup"><span data-stu-id="273fc-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="273fc-174">如果组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时调用 [Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="273fc-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="273fc-175">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="273fc-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="273fc-176">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="273fc-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="273fc-177">`StateHasChanged` 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="273fc-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="273fc-178">取消订阅 .NET 事件中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="273fc-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="273fc-179">下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何解除挂接 `Dispose` 方法中的事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="273fc-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="273fc-180">专用字段和 Lambda 方法</span><span class="sxs-lookup"><span data-stu-id="273fc-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="273fc-181">专用方法</span><span class="sxs-lookup"><span data-stu-id="273fc-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="273fc-182">处理错误</span><span class="sxs-lookup"><span data-stu-id="273fc-182">Handle errors</span></span>

<span data-ttu-id="273fc-183">有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="273fc-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="273fc-184">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="273fc-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="273fc-185">在 Blazor Server 应用中，当 `RenderMode` 为 `ServerPrerendered` 时，组件最初作为页面的一部分静态呈现。</span><span class="sxs-lookup"><span data-stu-id="273fc-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="273fc-186">浏览器重新建立与服务器的连接后，将*再次*呈现组件，并且该组件现在为交互式。</span><span class="sxs-lookup"><span data-stu-id="273fc-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="273fc-187">如果存在用于初始化组件的 [OnInitialized{Async}](#component-initialization-methods) 生命周期方法，则该方法执行*两次*：</span><span class="sxs-lookup"><span data-stu-id="273fc-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="273fc-188">在静态预呈现组件时执行一次。</span><span class="sxs-lookup"><span data-stu-id="273fc-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="273fc-189">在建立服务器连接后执行一次。</span><span class="sxs-lookup"><span data-stu-id="273fc-189">After the server connection has been established.</span></span>

<span data-ttu-id="273fc-190">在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。</span><span class="sxs-lookup"><span data-stu-id="273fc-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="273fc-191">若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="273fc-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="273fc-192">传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。</span><span class="sxs-lookup"><span data-stu-id="273fc-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="273fc-193">在预呈现期间使用该标识符保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="273fc-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="273fc-194">预呈现后使用该标识符检索缓存的状态。</span><span class="sxs-lookup"><span data-stu-id="273fc-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="273fc-195">以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现：</span><span class="sxs-lookup"><span data-stu-id="273fc-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="273fc-196">有关 `RenderMode` 的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="273fc-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="273fc-197">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="273fc-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="273fc-198">可取消的后台工作</span><span class="sxs-lookup"><span data-stu-id="273fc-198">Cancelable background work</span></span>

<span data-ttu-id="273fc-199">组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="273fc-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="273fc-200">在几种情况下，最好停止后台工作以节省系统资源。</span><span class="sxs-lookup"><span data-stu-id="273fc-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="273fc-201">例如，当用户离开组件时，后台异步操作不会自动停止。</span><span class="sxs-lookup"><span data-stu-id="273fc-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="273fc-202">后台工作项可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="273fc-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="273fc-203">正在执行的后台任务由错误的输入数据或处理参数启动。</span><span class="sxs-lookup"><span data-stu-id="273fc-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="273fc-204">正在执行的一组后台工作项必须替换为一组新的工作项。</span><span class="sxs-lookup"><span data-stu-id="273fc-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="273fc-205">必须更改当前正在执行的任务的优先级。</span><span class="sxs-lookup"><span data-stu-id="273fc-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="273fc-206">必须关闭应用才能将其重新部署到服务器。</span><span class="sxs-lookup"><span data-stu-id="273fc-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="273fc-207">服务器资源受到限制，需要重新计划后台工作项。</span><span class="sxs-lookup"><span data-stu-id="273fc-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="273fc-208">要在组件中实现可取消的后台工作模式：</span><span class="sxs-lookup"><span data-stu-id="273fc-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="273fc-209">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="273fc-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="273fc-210">在[释放组件](#component-disposal-with-idisposable)时，以及需要随时通过手动取消标记进行取消时，请调用 [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示应取消后台工作。</span><span class="sxs-lookup"><span data-stu-id="273fc-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="273fc-211">异步调用返回后，对该标记调用 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>。</span><span class="sxs-lookup"><span data-stu-id="273fc-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="273fc-212">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="273fc-212">In the following example:</span></span>

* <span data-ttu-id="273fc-213">`await Task.Delay(5000, cts.Token);` 表示长时间运行的异步后台工作。</span><span class="sxs-lookup"><span data-stu-id="273fc-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="273fc-214">`BackgroundResourceMethod` 表示如果在调用方法之前释放 `Resource`，则不应启动的长时间运行的后台方法。</span><span class="sxs-lookup"><span data-stu-id="273fc-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
