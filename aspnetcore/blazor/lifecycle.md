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
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213384"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="df914-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="df914-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="df914-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df914-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="df914-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="df914-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="df914-106">重写生命周期方法，以便在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="df914-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="df914-107">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="df914-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="df914-108">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="df914-108">Component initialization methods</span></span>

<span data-ttu-id="df914-109">当组件从其父组件收到其初始参数后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>。</span><span class="sxs-lookup"><span data-stu-id="df914-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="df914-110">当组件执行异步操作时，请使用 `OnInitializedAsync`，并在操作完成时进行刷新。</span><span class="sxs-lookup"><span data-stu-id="df914-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="df914-111">对于同步操作，请重写 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="df914-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="df914-112">若要执行异步操作，请重写 `OnInitializedAsync`，并对操作使用 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="df914-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="df914-113"> Server apps 预[呈现其内容](xref:blazor/hosting-model-configuration#render-mode)调用 `OnInitializedAsync` **_两次_** ：</span><span class="sxs-lookup"><span data-stu-id="df914-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="df914-114">作为页的一部分，最初以静态方式呈现组件时。</span><span class="sxs-lookup"><span data-stu-id="df914-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="df914-115">第二次当浏览器与服务器建立连接时。</span><span class="sxs-lookup"><span data-stu-id="df914-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="df914-116">若要防止 `OnInitializedAsync` 中的开发人员代码运行两次，请参阅预[呈现后](#stateful-reconnection-after-prerendering)的有状态重新连接部分。</span><span class="sxs-lookup"><span data-stu-id="df914-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="df914-117">在预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。</span><span class="sxs-lookup"><span data-stu-id="df914-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="df914-118">在预呈现时，组件可能需要以不同的方式呈现。</span><span class="sxs-lookup"><span data-stu-id="df914-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="df914-119">有关详细信息，请参阅[检测应用程序何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="df914-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="df914-120">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="df914-120">Before parameters are set</span></span>

<span data-ttu-id="df914-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 设置由呈现树中的组件父级提供的参数：</span><span class="sxs-lookup"><span data-stu-id="df914-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="df914-122"><xref:Microsoft.AspNetCore.Components.ParameterView> 包含每次调用 `SetParametersAsync` 时的完整参数值集。</span><span class="sxs-lookup"><span data-stu-id="df914-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="df914-123">`SetParametersAsync` 的默认实现将每个属性的值设置为在 `ParameterView`中具有相应值的 `[Parameter]` 或 `[CascadingParameter]` 特性。</span><span class="sxs-lookup"><span data-stu-id="df914-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="df914-124">在 `ParameterView` 中没有相应值的参数将保持不变。</span><span class="sxs-lookup"><span data-stu-id="df914-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="df914-125">如果未调用 `base.SetParametersAync`，则自定义代码可以通过任何所需的方式解释传入参数值。</span><span class="sxs-lookup"><span data-stu-id="df914-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="df914-126">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="df914-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="df914-127">设置参数后</span><span class="sxs-lookup"><span data-stu-id="df914-127">After parameters are set</span></span>

<span data-ttu-id="df914-128">调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>：</span><span class="sxs-lookup"><span data-stu-id="df914-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="df914-129">当组件已初始化并收到其父组件的第一组参数时。</span><span class="sxs-lookup"><span data-stu-id="df914-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="df914-130">当父组件重新呈现并提供时：</span><span class="sxs-lookup"><span data-stu-id="df914-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="df914-131">仅有至少一个参数发生更改的已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="df914-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="df914-132">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="df914-132">Any complex-typed parameters.</span></span> <span data-ttu-id="df914-133">此框架无法知道复杂类型参数的值是否在内部转变，因此它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="df914-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="df914-134">应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="df914-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="df914-135">组件呈现后</span><span class="sxs-lookup"><span data-stu-id="df914-135">After component render</span></span>

<span data-ttu-id="df914-136">当组件完成呈现后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>。</span><span class="sxs-lookup"><span data-stu-id="df914-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="df914-137">此时将填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="df914-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="df914-138">使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="df914-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="df914-139">`OnAfterRenderAsync` 和 `OnAfterRender`的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="df914-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="df914-140">在第一次呈现组件实例时，设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="df914-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="df914-141">可用于确保仅执行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="df914-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="df914-142">在 `OnAfterRenderAsync` 生命周期事件期间，必须立即进行渲染后的异步工作。</span><span class="sxs-lookup"><span data-stu-id="df914-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="df914-143">即使从 `OnAfterRenderAsync`中返回 <xref:System.Threading.Tasks.Task>，该任务完成后，框架也不会为组件计划进一步的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="df914-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="df914-144">这是为了避免出现无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="df914-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="df914-145">它与其他生命周期方法不同，后者将在返回的任务完成后计划更多的渲染循环。</span><span class="sxs-lookup"><span data-stu-id="df914-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="df914-146">在*服务器上预呈现时不会调用*`OnAfterRender` 和 `OnAfterRenderAsync`。</span><span class="sxs-lookup"><span data-stu-id="df914-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="df914-147">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="df914-147">Suppress UI refreshing</span></span>

<span data-ttu-id="df914-148">重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以取消 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="df914-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="df914-149">如果实现返回 `true`，则将刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="df914-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="df914-150">每次呈现组件时，都将调用 `ShouldRender`。</span><span class="sxs-lookup"><span data-stu-id="df914-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="df914-151">即使 `ShouldRender` 被重写，组件也始终是最初呈现的。</span><span class="sxs-lookup"><span data-stu-id="df914-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="df914-152">状态更改</span><span class="sxs-lookup"><span data-stu-id="df914-152">State changes</span></span>

<span data-ttu-id="df914-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件状态已更改。</span><span class="sxs-lookup"><span data-stu-id="df914-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="df914-154">如果适用，调用 `StateHasChanged` 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="df914-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="df914-155">在呈现时处理未完成的异步操作</span><span class="sxs-lookup"><span data-stu-id="df914-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="df914-156">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="df914-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="df914-157">在执行生命周期方法时，对象可能 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="df914-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="df914-158">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="df914-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="df914-159">`null`对象时呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="df914-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="df914-160">在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。</span><span class="sxs-lookup"><span data-stu-id="df914-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="df914-161">`null``forecasts` 时，将向用户显示一条加载消息。</span><span class="sxs-lookup"><span data-stu-id="df914-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="df914-162">`OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。</span><span class="sxs-lookup"><span data-stu-id="df914-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="df914-163">*页面/FetchData* Blazor 服务器模板：</span><span class="sxs-lookup"><span data-stu-id="df914-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="df914-164">用 IDisposable 进行的组件处置</span><span class="sxs-lookup"><span data-stu-id="df914-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="df914-165">如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="df914-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="df914-166">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="df914-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="df914-167">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。</span><span class="sxs-lookup"><span data-stu-id="df914-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="df914-168">`StateHasChanged` 可以作为撕裂渲染器的一部分被调用，因此不支持在该点请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="df914-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="df914-169">处理错误</span><span class="sxs-lookup"><span data-stu-id="df914-169">Handle errors</span></span>

<span data-ttu-id="df914-170">有关在执行生命周期方法期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="df914-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="df914-171">预呈现后有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="df914-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="df914-172">当 `RenderMode` `ServerPrerendered`时，在 Blazor Server 应用程序中，最初以页面的形式呈现组件。</span><span class="sxs-lookup"><span data-stu-id="df914-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="df914-173">一旦浏览器与服务器建立了连接，该组件将*再次*呈现，并且该组件现在是交互式的。</span><span class="sxs-lookup"><span data-stu-id="df914-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="df914-174">如果存在用于初始化组件的[OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods)生命周期方法，则将执行*两次*此方法：</span><span class="sxs-lookup"><span data-stu-id="df914-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="df914-175">如果组件预呈现静态，则为。</span><span class="sxs-lookup"><span data-stu-id="df914-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="df914-176">建立服务器连接之后。</span><span class="sxs-lookup"><span data-stu-id="df914-176">After the server connection has been established.</span></span>

<span data-ttu-id="df914-177">这可能会导致在最终呈现组件时，UI 中显示的数据发生显著变化。</span><span class="sxs-lookup"><span data-stu-id="df914-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="df914-178">若要避免 Blazor 服务器应用中出现双重渲染方案：</span><span class="sxs-lookup"><span data-stu-id="df914-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="df914-179">传入一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重新启动后检索状态。</span><span class="sxs-lookup"><span data-stu-id="df914-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="df914-180">在预呈现期间使用标识符以保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="df914-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="df914-181">在预呈现后使用标识符检索缓存状态。</span><span class="sxs-lookup"><span data-stu-id="df914-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="df914-182">下面的代码演示基于模板的 Blazor 服务器应用中的更新 `WeatherForecastService`，可避免双重呈现：</span><span class="sxs-lookup"><span data-stu-id="df914-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="df914-183">有关 `RenderMode`的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="df914-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="df914-184">检测预呈现应用的时间</span><span class="sxs-lookup"><span data-stu-id="df914-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
