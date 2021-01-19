---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: e5f9a07db742ce2e26f03c0b6e1caa1904e4e0d9
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058228"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="49317-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="49317-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="49317-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="49317-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="49317-105">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="49317-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="49317-106">替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="49317-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="49317-107">下图展示的是 Blazor 生命周期。</span><span class="sxs-lookup"><span data-stu-id="49317-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="49317-108">本文以下部分中的示例定义了生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="49317-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="49317-109">组件生命周期事件：</span><span class="sxs-lookup"><span data-stu-id="49317-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="49317-110">如果组件是第一次呈现在请求上：</span><span class="sxs-lookup"><span data-stu-id="49317-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="49317-111">创建组件的实例。</span><span class="sxs-lookup"><span data-stu-id="49317-111">Create the component's instance.</span></span>
   * <span data-ttu-id="49317-112">执行属性注入。</span><span class="sxs-lookup"><span data-stu-id="49317-112">Perform property injection.</span></span> <span data-ttu-id="49317-113">运行 `SetParametersAsync`。</span><span class="sxs-lookup"><span data-stu-id="49317-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="49317-114">调用 [`OnInitialized{Async}`](#component-initialization-methods)。</span><span class="sxs-lookup"><span data-stu-id="49317-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="49317-115">如果返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="49317-116">如果未返回 <xref:System.Threading.Tasks.Task>，则呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="49317-117">调用 [`OnParametersSet{Async}`](#after-parameters-are-set) 并呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="49317-118">如果 `OnParametersSetAsync` 返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor 中 Razor 组件的组件生命周期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="49317-120">文档对象模型 (DOM) 事件处理：</span><span class="sxs-lookup"><span data-stu-id="49317-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="49317-121">运行事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="49317-121">The event handler is run.</span></span>
1. <span data-ttu-id="49317-122">如果返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="49317-123">如果未返回 <xref:System.Threading.Tasks.Task>，则呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![文档对象模型 (DOM) 事件处理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="49317-125">`Render` 生命周期：</span><span class="sxs-lookup"><span data-stu-id="49317-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="49317-126">停止组件的进一步呈现操作：</span><span class="sxs-lookup"><span data-stu-id="49317-126">Stop further rendering operations on the component:</span></span>
   * <span data-ttu-id="49317-127">在第一次呈现后。</span><span class="sxs-lookup"><span data-stu-id="49317-127">After the first render.</span></span>
   * <span data-ttu-id="49317-128">当 [`ShouldRender`](#suppress-ui-refreshing) 为 `false` 时。</span><span class="sxs-lookup"><span data-stu-id="49317-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="49317-129">生成呈现树差异并呈现组件。</span><span class="sxs-lookup"><span data-stu-id="49317-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="49317-130">等待 DOM 更新。</span><span class="sxs-lookup"><span data-stu-id="49317-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="49317-131">调用 [`OnAfterRender{Async}`](#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="49317-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![呈现生命周期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="49317-133">开发人员调用 [`StateHasChanged`](#state-changes) 会产生呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="49317-134">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="49317-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="49317-135">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="49317-135">Before parameters are set</span></span>

<span data-ttu-id="49317-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在呈现树中设置组件的父组件提供的参数：</span><span class="sxs-lookup"><span data-stu-id="49317-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="49317-137">每次调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含该组件的参数值集。</span><span class="sxs-lookup"><span data-stu-id="49317-137"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="49317-138"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 的默认实现使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 特性（在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中具有对应的值）设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="49317-138">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="49317-139">在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中没有对应值的参数保持不变。</span><span class="sxs-lookup"><span data-stu-id="49317-139">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="49317-140">如果未调用 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)，则自定义代码可使用任何需要的方式解释传入的参数值。</span><span class="sxs-lookup"><span data-stu-id="49317-140">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="49317-141">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="49317-141">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="49317-142">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="49317-142">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49317-143">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-143">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="49317-144">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="49317-144">Component initialization methods</span></span>

<span data-ttu-id="49317-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 中的组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="49317-146">在组件执行异步操作时使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>，并应在操作完成后刷新。</span><span class="sxs-lookup"><span data-stu-id="49317-146">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="49317-147">对于同步操作，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>：</span><span class="sxs-lookup"><span data-stu-id="49317-147">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="49317-148">若要执行异步操作，请替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 并对该操作使用 [`await`](/dotnet/csharp/language-reference/operators/await) 运算符：</span><span class="sxs-lookup"><span data-stu-id="49317-148">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="49317-149">[预呈现其内容](xref:blazor/fundamentals/additional-scenarios#render-mode)的 Blazor Server 应用调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 两次：</span><span class="sxs-lookup"><span data-stu-id="49317-149">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="49317-150">在组件最初作为页面的一部分静态呈现时调用一次。</span><span class="sxs-lookup"><span data-stu-id="49317-150">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="49317-151">在浏览器重新建立与服务器的连接时调用第二次。</span><span class="sxs-lookup"><span data-stu-id="49317-151">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="49317-152">为了防止 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-152">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="49317-153">在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行调用 JavaScript 等特定操作。</span><span class="sxs-lookup"><span data-stu-id="49317-153">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="49317-154">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-154">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="49317-155">有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-155">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="49317-156">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="49317-156">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49317-157">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-157">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="49317-158">设置参数之后</span><span class="sxs-lookup"><span data-stu-id="49317-158">After parameters are set</span></span>

<span data-ttu-id="49317-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 在以下情况下调用：</span><span class="sxs-lookup"><span data-stu-id="49317-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="49317-160">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中初始化组件后。</span><span class="sxs-lookup"><span data-stu-id="49317-160">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="49317-161">当父组件重新呈现并提供以下内容时：</span><span class="sxs-lookup"><span data-stu-id="49317-161">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="49317-162">至少一个参数已更改的唯一已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="49317-162">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="49317-163">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="49317-163">Any complex-typed parameters.</span></span> <span data-ttu-id="49317-164">框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="49317-164">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="49317-165">应用参数和属性值时，异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="49317-165">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="49317-166">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="49317-166">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49317-167">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-167">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="49317-168">组件呈现之后</span><span class="sxs-lookup"><span data-stu-id="49317-168">After component render</span></span>

<span data-ttu-id="49317-169"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="49317-169"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="49317-170">此时会填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="49317-170">Element and component references are populated at this point.</span></span> <span data-ttu-id="49317-171">在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="49317-171">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="49317-172"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="49317-172">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="49317-173">在第一次呈现组件实例时设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="49317-173">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="49317-174">可用于确保初始化操作仅执行一次。</span><span class="sxs-lookup"><span data-stu-id="49317-174">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="49317-175">呈现后立即进行的异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="49317-175">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="49317-176">即使从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。</span><span class="sxs-lookup"><span data-stu-id="49317-176">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="49317-177">这是为了避免无限呈现循环。</span><span class="sxs-lookup"><span data-stu-id="49317-177">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="49317-178">它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。</span><span class="sxs-lookup"><span data-stu-id="49317-178">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="49317-179">在服务器上的预呈现过程中，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="49317-180">在预呈现完成后以交互方式呈现组件时，将调用这些方法。</span><span class="sxs-lookup"><span data-stu-id="49317-180">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="49317-181">当应用预呈现时：</span><span class="sxs-lookup"><span data-stu-id="49317-181">When the app prerenders:</span></span>

1. <span data-ttu-id="49317-182">组件将在服务器上执行，以在 HTTP 响应中生成一些静态 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="49317-182">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="49317-183">在此阶段，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-183">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="49317-184">当 `blazor.server.js` 或 `blazor.webassembly.js` 在浏览器中启动时，组件将以交互呈现模式重新启动。</span><span class="sxs-lookup"><span data-stu-id="49317-184">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="49317-185">组件重新启动后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>，因为应用不再处于预呈现阶段。</span><span class="sxs-lookup"><span data-stu-id="49317-185">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="49317-186">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="49317-186">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="49317-187">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="49317-187">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="49317-188">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="49317-188">Suppress UI refreshing</span></span>

<span data-ttu-id="49317-189">替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以禁止 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="49317-189">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="49317-190">如果实现返回 `true`，则刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="49317-190">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="49317-191">每次呈现组件时都会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="49317-192">即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被替代，组件也始终在最初呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-192">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="49317-193">有关详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。</span><span class="sxs-lookup"><span data-stu-id="49317-193">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="49317-194">状态更改</span><span class="sxs-lookup"><span data-stu-id="49317-194">State changes</span></span>

<span data-ttu-id="49317-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。</span><span class="sxs-lookup"><span data-stu-id="49317-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="49317-196">如果适用，调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-196">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="49317-197">将自动为 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-197"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="49317-198">有关详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="49317-198">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="49317-199">处理呈现时的不完整异步操作</span><span class="sxs-lookup"><span data-stu-id="49317-199">Handle incomplete async actions at render</span></span>

<span data-ttu-id="49317-200">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="49317-200">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="49317-201">执行生命周期方法时，对象可能为 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="49317-201">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="49317-202">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="49317-202">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="49317-203">对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="49317-203">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="49317-204">在 Blazor 模板的 `FetchData` 组件中，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 以异步接收预测数据 (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="49317-204">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="49317-205">当 `forecasts` 为 `null` 时，将向用户显示加载消息。</span><span class="sxs-lookup"><span data-stu-id="49317-205">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="49317-206"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-206">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="49317-207">Blazor Server 模板中的 `Pages/FetchData.razor`：</span><span class="sxs-lookup"><span data-stu-id="49317-207">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="49317-208">处理错误</span><span class="sxs-lookup"><span data-stu-id="49317-208">Handle errors</span></span>

<span data-ttu-id="49317-209">有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/fundamentals/handle-errors#lifecycle-methods>。</span><span class="sxs-lookup"><span data-stu-id="49317-209">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="49317-210">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="49317-210">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="49317-211">在 Blazor Server 应用中，当 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> 时，组件最初作为页面的一部分静态呈现。</span><span class="sxs-lookup"><span data-stu-id="49317-211">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="49317-212">浏览器重新建立与服务器的连接后，将 *再次* 呈现组件，并且该组件现在为交互式。</span><span class="sxs-lookup"><span data-stu-id="49317-212">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="49317-213">如果存在用于初始化组件的 [`OnInitialized{Async}`](#component-initialization-methods) 生命周期方法，则该方法执行两次：</span><span class="sxs-lookup"><span data-stu-id="49317-213">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="49317-214">在静态预呈现组件时执行一次。</span><span class="sxs-lookup"><span data-stu-id="49317-214">When the component is prerendered statically.</span></span>
* <span data-ttu-id="49317-215">在建立服务器连接后执行一次。</span><span class="sxs-lookup"><span data-stu-id="49317-215">After the server connection has been established.</span></span>

<span data-ttu-id="49317-216">在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。</span><span class="sxs-lookup"><span data-stu-id="49317-216">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="49317-217">若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="49317-217">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="49317-218">传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。</span><span class="sxs-lookup"><span data-stu-id="49317-218">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="49317-219">在预呈现期间使用该标识符保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="49317-219">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="49317-220">预呈现后使用该标识符检索缓存的状态。</span><span class="sxs-lookup"><span data-stu-id="49317-220">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="49317-221">以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现：</span><span class="sxs-lookup"><span data-stu-id="49317-221">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="49317-222">有关 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 的详细信息，请参阅 <xref:blazor/fundamentals/additional-scenarios#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="49317-222">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="49317-223">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="49317-223">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="49317-224">使用 IDisposable 处置组件</span><span class="sxs-lookup"><span data-stu-id="49317-224">Component disposal with IDisposable</span></span>

<span data-ttu-id="49317-225">如果组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时调用 [`Dispose` 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="49317-225">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="49317-226">可随时进行处置，包括在[组件初始化](#component-initialization-methods)期间。</span><span class="sxs-lookup"><span data-stu-id="49317-226">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="49317-227">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="49317-227">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="49317-228">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-228">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="49317-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="49317-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="49317-230">取消订阅 .NET 事件中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="49317-230">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="49317-231">下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何解除挂接 `Dispose` 方法中的事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="49317-231">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="49317-232">专用字段和 Lambda 方法</span><span class="sxs-lookup"><span data-stu-id="49317-232">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="49317-233">专用方法</span><span class="sxs-lookup"><span data-stu-id="49317-233">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="49317-234">可取消的后台工作</span><span class="sxs-lookup"><span data-stu-id="49317-234">Cancelable background work</span></span>

<span data-ttu-id="49317-235">组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="49317-235">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="49317-236">在几种情况下，最好停止后台工作以节省系统资源。</span><span class="sxs-lookup"><span data-stu-id="49317-236">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="49317-237">例如，当用户离开组件时，后台异步操作不会自动停止。</span><span class="sxs-lookup"><span data-stu-id="49317-237">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="49317-238">后台工作项可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="49317-238">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="49317-239">正在执行的后台任务由错误的输入数据或处理参数启动。</span><span class="sxs-lookup"><span data-stu-id="49317-239">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="49317-240">正在执行的一组后台工作项必须替换为一组新的工作项。</span><span class="sxs-lookup"><span data-stu-id="49317-240">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="49317-241">必须更改当前正在执行的任务的优先级。</span><span class="sxs-lookup"><span data-stu-id="49317-241">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="49317-242">必须关闭应用才能将其重新部署到服务器。</span><span class="sxs-lookup"><span data-stu-id="49317-242">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="49317-243">服务器资源受到限制，需要重新计划后台工作项。</span><span class="sxs-lookup"><span data-stu-id="49317-243">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="49317-244">要在组件中实现可取消的后台工作模式：</span><span class="sxs-lookup"><span data-stu-id="49317-244">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="49317-245">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="49317-245">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="49317-246">在[释放组件](#component-disposal-with-idisposable)时，以及需要随时通过手动取消标记进行取消时，请调用 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示应取消后台工作。</span><span class="sxs-lookup"><span data-stu-id="49317-246">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="49317-247">异步调用返回后，对该标记调用 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>。</span><span class="sxs-lookup"><span data-stu-id="49317-247">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="49317-248">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="49317-248">In the following example:</span></span>

* <span data-ttu-id="49317-249">`await Task.Delay(5000, cts.Token);` 表示长时间运行的异步后台工作。</span><span class="sxs-lookup"><span data-stu-id="49317-249">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="49317-250">`BackgroundResourceMethod` 表示如果在调用方法之前释放 `Resource`，则不应启动的长时间运行的后台方法。</span><span class="sxs-lookup"><span data-stu-id="49317-250">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="49317-251">Blazor Server 重新连接事件</span><span class="sxs-lookup"><span data-stu-id="49317-251">Blazor Server reconnection events</span></span>

<span data-ttu-id="49317-252">本文所述的组件生命周期事件与 [Blazor Server 的重新连接事件处理程序](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui)分开运行。</span><span class="sxs-lookup"><span data-stu-id="49317-252">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="49317-253">当 Blazor Server 应用断开其与客户端的 SignalR 连接时，只有 UI 更新会被中断。</span><span class="sxs-lookup"><span data-stu-id="49317-253">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="49317-254">重新建立连接后，将恢复 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="49317-254">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="49317-255">有关线路处理程序事件和配置的详细信息，请参阅 <xref:blazor/fundamentals/additional-scenarios>。</span><span class="sxs-lookup"><span data-stu-id="49317-255">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
