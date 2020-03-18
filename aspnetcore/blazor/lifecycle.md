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
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647580"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="8bb51-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="8bb51-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="8bb51-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8bb51-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8bb51-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="8bb51-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="8bb51-106">替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="8bb51-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="8bb51-107">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="8bb51-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="8bb51-108">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="8bb51-108">Component initialization methods</span></span>

<span data-ttu-id="8bb51-109">组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>。</span><span class="sxs-lookup"><span data-stu-id="8bb51-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="8bb51-110">在组件执行异步操作时使用 `OnInitializedAsync`，并应在操作完成后刷新。</span><span class="sxs-lookup"><span data-stu-id="8bb51-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="8bb51-111">对于同步操作，替代 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="8bb51-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="8bb51-112">若要执行异步操作，请替代 `OnInitializedAsync` 并对该操作使用 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="8bb51-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="8bb51-113">[预呈现其内容](xref:blazor/hosting-model-configuration#render-mode)的 Blazor Server 应用调用 `OnInitializedAsync` **_两次_** ：</span><span class="sxs-lookup"><span data-stu-id="8bb51-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="8bb51-114">在组件最初作为页面的一部分静态呈现时调用一次。</span><span class="sxs-lookup"><span data-stu-id="8bb51-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="8bb51-115">在浏览器重新建立与服务器的连接时调用第二次。</span><span class="sxs-lookup"><span data-stu-id="8bb51-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="8bb51-116">为了防止 `OnInitializedAsync` 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="8bb51-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="8bb51-117">在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行特定操作（例如调用 JavaScript）。</span><span class="sxs-lookup"><span data-stu-id="8bb51-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="8bb51-118">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="8bb51-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="8bb51-119">有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="8bb51-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="8bb51-120">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="8bb51-120">Before parameters are set</span></span>

<span data-ttu-id="8bb51-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 在呈现树中设置组件的父组件提供的参数：</span><span class="sxs-lookup"><span data-stu-id="8bb51-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="8bb51-122">每次调用 `SetParametersAsync` 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含整个参数值集。</span><span class="sxs-lookup"><span data-stu-id="8bb51-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="8bb51-123">`SetParametersAsync` 的默认实现使用 `[Parameter]` 或 `[CascadingParameter]` 特性（在 `ParameterView` 中具有对应的值）设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="8bb51-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="8bb51-124">在 `ParameterView` 中没有对应值的参数保持不变。</span><span class="sxs-lookup"><span data-stu-id="8bb51-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="8bb51-125">如果未调用 `base.SetParametersAync`，则自定义代码可使用任何需要的方式解释传入的参数值。</span><span class="sxs-lookup"><span data-stu-id="8bb51-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="8bb51-126">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="8bb51-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="8bb51-127">设置参数之后</span><span class="sxs-lookup"><span data-stu-id="8bb51-127">After parameters are set</span></span>

<span data-ttu-id="8bb51-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> 在以下情况下调用：</span><span class="sxs-lookup"><span data-stu-id="8bb51-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="8bb51-129">当组件被初始化并从其父组件收到其第一组参数时。</span><span class="sxs-lookup"><span data-stu-id="8bb51-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="8bb51-130">当父组件重新呈现并提供以下内容时：</span><span class="sxs-lookup"><span data-stu-id="8bb51-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="8bb51-131">至少一个参数已更改的唯一已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="8bb51-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="8bb51-132">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="8bb51-132">Any complex-typed parameters.</span></span> <span data-ttu-id="8bb51-133">框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="8bb51-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="8bb51-134">应用参数和属性值时，异步操作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="8bb51-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="8bb51-135">组件呈现之后</span><span class="sxs-lookup"><span data-stu-id="8bb51-135">After component render</span></span>

<span data-ttu-id="8bb51-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="8bb51-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="8bb51-137">此时会填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="8bb51-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="8bb51-138">在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="8bb51-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="8bb51-139">`OnAfterRenderAsync` 和 `OnAfterRender` 的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="8bb51-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="8bb51-140">在第一次呈现组件实例时设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="8bb51-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="8bb51-141">可用于确保初始化操作仅执行一次。</span><span class="sxs-lookup"><span data-stu-id="8bb51-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="8bb51-142">呈现后立即进行的异步操作必须在 `OnAfterRenderAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="8bb51-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="8bb51-143">即使从 `OnAfterRenderAsync` 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。</span><span class="sxs-lookup"><span data-stu-id="8bb51-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="8bb51-144">这是为了避免无限呈现循环。</span><span class="sxs-lookup"><span data-stu-id="8bb51-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="8bb51-145">它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。</span><span class="sxs-lookup"><span data-stu-id="8bb51-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="8bb51-146">*在服务器上进行预呈现时*未调用 `OnAfterRender` 和 `OnAfterRenderAsync`。</span><span class="sxs-lookup"><span data-stu-id="8bb51-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="8bb51-147">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="8bb51-147">Suppress UI refreshing</span></span>

<span data-ttu-id="8bb51-148">替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以禁止 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="8bb51-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="8bb51-149">如果实现返回 `true`，则刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="8bb51-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="8bb51-150">每次呈现组件时都会调用 `ShouldRender`。</span><span class="sxs-lookup"><span data-stu-id="8bb51-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="8bb51-151">即使 `ShouldRender` 被替代，组件也始终在最初呈现。</span><span class="sxs-lookup"><span data-stu-id="8bb51-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="8bb51-152">状态更改</span><span class="sxs-lookup"><span data-stu-id="8bb51-152">State changes</span></span>

<span data-ttu-id="8bb51-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件其状态已更改。</span><span class="sxs-lookup"><span data-stu-id="8bb51-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="8bb51-154">如果适用，调用 `StateHasChanged` 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="8bb51-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="8bb51-155">处理呈现时的不完整异步操作</span><span class="sxs-lookup"><span data-stu-id="8bb51-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="8bb51-156">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="8bb51-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="8bb51-157">执行生命周期方法时，对象可能为 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="8bb51-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="8bb51-158">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="8bb51-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="8bb51-159">对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="8bb51-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="8bb51-160">在 Blazor 模板的 `FetchData` 组件中，替代 `OnInitializedAsync` 以异步接收预测数据 (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="8bb51-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="8bb51-161">当 `forecasts` 为 `null` 时，将向用户显示加载消息。</span><span class="sxs-lookup"><span data-stu-id="8bb51-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="8bb51-162">`OnInitializedAsync` 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。</span><span class="sxs-lookup"><span data-stu-id="8bb51-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="8bb51-163">Blazor Server 模板中的 *Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="8bb51-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="8bb51-164">使用 IDisposable 处置组件</span><span class="sxs-lookup"><span data-stu-id="8bb51-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="8bb51-165">如果组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时调用 [Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="8bb51-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="8bb51-166">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="8bb51-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="8bb51-167">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。</span><span class="sxs-lookup"><span data-stu-id="8bb51-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="8bb51-168">`StateHasChanged` 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="8bb51-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="8bb51-169">处理错误</span><span class="sxs-lookup"><span data-stu-id="8bb51-169">Handle errors</span></span>

<span data-ttu-id="8bb51-170">有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="8bb51-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="8bb51-171">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="8bb51-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="8bb51-172">在 Blazor Server 应用中，当 `RenderMode` 为 `ServerPrerendered` 时，组件最初作为页面的一部分静态呈现。</span><span class="sxs-lookup"><span data-stu-id="8bb51-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="8bb51-173">浏览器重新建立与服务器的连接后，将*再次*呈现组件，并且该组件现在为交互式。</span><span class="sxs-lookup"><span data-stu-id="8bb51-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="8bb51-174">如果存在用于初始化组件的 [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) 生命周期方法，则该方法执行*两次*：</span><span class="sxs-lookup"><span data-stu-id="8bb51-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="8bb51-175">在静态预呈现组件时执行一次。</span><span class="sxs-lookup"><span data-stu-id="8bb51-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="8bb51-176">在建立服务器连接后执行一次。</span><span class="sxs-lookup"><span data-stu-id="8bb51-176">After the server connection has been established.</span></span>

<span data-ttu-id="8bb51-177">在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。</span><span class="sxs-lookup"><span data-stu-id="8bb51-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="8bb51-178">若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8bb51-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="8bb51-179">传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。</span><span class="sxs-lookup"><span data-stu-id="8bb51-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="8bb51-180">在预呈现期间使用该标识符保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="8bb51-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="8bb51-181">预呈现后使用该标识符检索缓存的状态。</span><span class="sxs-lookup"><span data-stu-id="8bb51-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="8bb51-182">以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现：</span><span class="sxs-lookup"><span data-stu-id="8bb51-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="8bb51-183">有关 `RenderMode` 的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="8bb51-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="8bb51-184">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="8bb51-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
