---
title: ASP.NET Core Blazor 组件呈现
author: guardrex
description: 了解 ASP.NET Core Blazor 应用中的 Razor 组件呈现，包括何时调用 StateHasChanged。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253827"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a><span data-ttu-id="b309e-103">ASP.NET Core Blazor 组件呈现</span><span class="sxs-lookup"><span data-stu-id="b309e-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="b309e-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="b309e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="b309e-105">当组件第一次通过其父组件添加到组件层次结构时，它们必须呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="b309e-106">严格来说，只有在这种情况下，组件才必须呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="b309e-107">在其他时间，组件可以根据自己的逻辑和约定选择呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="b309e-108">`ComponentBase` 约定</span><span class="sxs-lookup"><span data-stu-id="b309e-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="b309e-109">默认情况下，Razor 组件 (`.razor`) 继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基类，该基类包含在以下时间触发重新呈现的逻辑：</span><span class="sxs-lookup"><span data-stu-id="b309e-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="b309e-110">从父组件应用一组已更新的参数之后。</span><span class="sxs-lookup"><span data-stu-id="b309e-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="b309e-111">为级联参数应用已更新的值之后。</span><span class="sxs-lookup"><span data-stu-id="b309e-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="b309e-112">通知事件并调用其自己的某个事件处理程序之后。</span><span class="sxs-lookup"><span data-stu-id="b309e-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="b309e-113">调用其自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之后。</span><span class="sxs-lookup"><span data-stu-id="b309e-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="b309e-114">如果满足以下任一条件，则继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的组件会跳过因参数更新而触发的重新呈现：</span><span class="sxs-lookup"><span data-stu-id="b309e-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="b309e-115">所有参数值都是已知的不可变基元类型（例如，`int`、`string`、`DateTime`），并且自上一组参数设置后就没有改变过。</span><span class="sxs-lookup"><span data-stu-id="b309e-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="b309e-116">组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="b309e-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="b309e-117">有关 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。</span><span class="sxs-lookup"><span data-stu-id="b309e-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="b309e-118">控制呈现流</span><span class="sxs-lookup"><span data-stu-id="b309e-118">Control the rendering flow</span></span>

<span data-ttu-id="b309e-119">在大多数情况下，<xref:Microsoft.AspNetCore.Components.ComponentBase> 约定会在某个事件发生后导致重新呈现正确的组件子集。</span><span class="sxs-lookup"><span data-stu-id="b309e-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="b309e-120">开发人员通常不需要提供手动逻辑来告诉框架，要重新呈现哪些组件以及何时重新呈现它们。</span><span class="sxs-lookup"><span data-stu-id="b309e-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="b309e-121">框架约定的总体效果如下：接收事件的组件重新呈现自身，从而递归触发参数值可能已更改的后代组件的重新呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="b309e-122">有关框架约定对性能的影响以及如何优化应用的组件层次结构的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>。</span><span class="sxs-lookup"><span data-stu-id="b309e-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="b309e-123">何时调用 `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="b309e-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="b309e-124">使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> 方法可以随时触发呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="b309e-125">但请注意，不必要时，不要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，这是一个常见错误，因为这会产生不必要的呈现成本。</span><span class="sxs-lookup"><span data-stu-id="b309e-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="b309e-126">在以下情况下，应该不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：</span><span class="sxs-lookup"><span data-stu-id="b309e-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="b309e-127">定期处理事件（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发大多数常规事件处理程序的呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="b309e-128">实现 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 等典型生命周期逻辑（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发典型生命周期事件的呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="b309e-129">但是，在以下部分所述的情况下，可能适合调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：</span><span class="sxs-lookup"><span data-stu-id="b309e-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="b309e-130">异步处理程序涉及多个异步阶段</span><span class="sxs-lookup"><span data-stu-id="b309e-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="b309e-131">从 Blazor 呈现和事件处理系统外部接收调用</span><span class="sxs-lookup"><span data-stu-id="b309e-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="b309e-132">在由特定事件重新呈现的子树之外呈现组件</span><span class="sxs-lookup"><span data-stu-id="b309e-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="b309e-133">异步处理程序涉及多个异步阶段</span><span class="sxs-lookup"><span data-stu-id="b309e-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="b309e-134">以下面的 `Counter` 组件为例，该组件在每次单击时都会更新计数四次。</span><span class="sxs-lookup"><span data-stu-id="b309e-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="b309e-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b309e-135">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="b309e-136">由于在 .NET 中定义任务的方式，<xref:System.Threading.Tasks.Task> 的接收方只能观察到其最终完成状态，而观察不到中间异步状态。</span><span class="sxs-lookup"><span data-stu-id="b309e-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="b309e-137">因此，仅在第一次返回 <xref:System.Threading.Tasks.Task> 且 <xref:System.Threading.Tasks.Task> 最终完成时，<xref:Microsoft.AspNetCore.Components.ComponentBase> 才能触发重新呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="b309e-138">它不知道在其他中间点重新呈现。</span><span class="sxs-lookup"><span data-stu-id="b309e-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="b309e-139">如果要在中间点重新呈现，请使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="b309e-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a><span data-ttu-id="b309e-140">从 Blazor 呈现和事件处理系统外部接收调用</span><span class="sxs-lookup"><span data-stu-id="b309e-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="b309e-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道其自身的生命周期方法和 Blazor 触发的事件。</span><span class="sxs-lookup"><span data-stu-id="b309e-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="b309e-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道代码中可能发生的其他事件。</span><span class="sxs-lookup"><span data-stu-id="b309e-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="b309e-143">例如，Blazor 不知道自定义数据存储引发的任何 C# 事件。</span><span class="sxs-lookup"><span data-stu-id="b309e-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="b309e-144">为了使此类事件触发重新呈现，从而在 UI 中显示已更新的值，请使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="b309e-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="b309e-145">在另一个用例中，以下面的 `Counter` 组件为例，该组件使用 <xref:System.Timers.Timer?displayProperty=fullName> 定期更新计数并调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 来更新 UI。</span><span class="sxs-lookup"><span data-stu-id="b309e-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="b309e-146">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b309e-146">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="b309e-147">在上述示例中，`OnTimerCallback` 必须调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，因为 Blazor 不知道回调中的 `currentCount` 更改。</span><span class="sxs-lookup"><span data-stu-id="b309e-147">In the preceding example, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="b309e-148">`OnTimerCallback` 在 Blazor 管理的任何呈现流或事件通知之外运行。</span><span class="sxs-lookup"><span data-stu-id="b309e-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>

<span data-ttu-id="b309e-149">同样，由于回调是在 Blazor 的同步上下文之外调用的，因此必须将逻辑包装在 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 中，以将其移到呈现器的同步上下文中。</span><span class="sxs-lookup"><span data-stu-id="b309e-149">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="b309e-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能从呈现器的同步上下文调用，否则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="b309e-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="b309e-151">这等效于封送到其他 UI 框架中的 UI 线程。</span><span class="sxs-lookup"><span data-stu-id="b309e-151">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="b309e-152">在由特定事件重新呈现的子树之外呈现组件</span><span class="sxs-lookup"><span data-stu-id="b309e-152">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="b309e-153">你的 UI 可能涉及将事件调度到一个组件，更改某种状态，并且需要重新呈现一个完全不同的组件，该组件不是接收事件的组件的后代。</span><span class="sxs-lookup"><span data-stu-id="b309e-153">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="b309e-154">应对这种情况的一种方法是，将状态管理类（可能作为 DI 服务）注入到多个组件中。</span><span class="sxs-lookup"><span data-stu-id="b309e-154">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="b309e-155">当一个组件在状态管理器上调用方法时，状态管理器可以引发 C# 事件，然后由独立组件接收该事件。</span><span class="sxs-lookup"><span data-stu-id="b309e-155">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="b309e-156">由于这些 C# 事件位于 Blazor 呈现管道之外，因此，请对要呈现的其他组件调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，以响应状态管理器的事件。</span><span class="sxs-lookup"><span data-stu-id="b309e-156">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="b309e-157">这与前面的 <xref:System.Timers.Timer?displayProperty=fullName> 案例（[上一部分](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中）类似。</span><span class="sxs-lookup"><span data-stu-id="b309e-157">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="b309e-158">由于执行调用堆栈通常保留在呈现器的同步上下文中，因此通常不需要 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="b309e-158">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="b309e-159">仅当逻辑转义同步上下文时才需要 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>，例如，对 <xref:System.Threading.Tasks.Task> 调用 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 或使用 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 等待 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="b309e-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
