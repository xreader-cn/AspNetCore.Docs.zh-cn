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
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280046"
---
# <a name="aspnet-core-blazor-component-rendering"></a>ASP.NET Core Blazor 组件呈现

当组件第一次通过其父组件添加到组件层次结构时，它们必须呈现。 严格来说，只有在这种情况下，组件才必须呈现。

在其他时间，组件可以根据自己的逻辑和约定选择呈现。

## <a name="conventions-for-componentbase"></a>`ComponentBase` 约定

默认情况下，Razor 组件 (`.razor`) 继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基类，该基类包含在以下时间触发重新呈现的逻辑：

* 从父组件应用一组已更新的参数之后。
* 为级联参数应用已更新的值之后。
* 通知事件并调用其自己的某个事件处理程序之后。
* 调用其自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之后。

如果满足以下任一条件，则继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的组件会跳过因参数更新而触发的重新呈现：

* 所有参数值都是已知的不可变基元类型（例如，`int`、`string`、`DateTime`），并且自上一组参数设置后就没有改变过。
* 组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法返回 `false`。

有关 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。

## <a name="control-the-rendering-flow"></a>控制呈现流

在大多数情况下，<xref:Microsoft.AspNetCore.Components.ComponentBase> 约定会在某个事件发生后导致重新呈现正确的组件子集。 开发人员通常不需要提供手动逻辑来告诉框架，要重新呈现哪些组件以及何时重新呈现它们。 框架约定的总体效果如下：接收事件的组件重新呈现自身，从而递归触发参数值可能已更改的后代组件的重新呈现。

有关框架约定对性能的影响以及如何优化应用的组件层次结构的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>。

## <a name="when-to-call-statehaschanged"></a>何时调用 `StateHasChanged`

使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> 方法可以随时触发呈现。 但请注意，不必要时，不要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，这是一个常见错误，因为这会产生不必要的呈现成本。

在以下情况下，应该不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：

* 定期处理事件（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发大多数常规事件处理程序的呈现。
* 实现 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 等典型生命周期逻辑（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发典型生命周期事件的呈现。

但是，在以下部分所述的情况下，可能适合调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：

* [异步处理程序涉及多个异步阶段](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [从 Blazor 呈现和事件处理系统外部接收调用](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [在由特定事件重新呈现的子树之外呈现组件](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>异步处理程序涉及多个异步阶段

以下面的 `Counter` 组件为例，该组件在每次单击时都会更新计数四次。

`Pages/Counter.razor`:

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

由于在 .NET 中定义任务的方式，<xref:System.Threading.Tasks.Task> 的接收方只能观察到其最终完成状态，而观察不到中间异步状态。 因此，仅在第一次返回 <xref:System.Threading.Tasks.Task> 且 <xref:System.Threading.Tasks.Task> 最终完成时，<xref:Microsoft.AspNetCore.Components.ComponentBase> 才能触发重新呈现。 它不知道在其他中间点重新呈现。 如果要在中间点重新呈现，请使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>从 Blazor 呈现和事件处理系统外部接收调用

<xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道其自身的生命周期方法和 Blazor 触发的事件。 <xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道代码中可能发生的其他事件。 例如，Blazor 不知道自定义数据存储引发的任何 C# 事件。 为了使此类事件触发重新呈现，从而在 UI 中显示已更新的值，请使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。

在另一个用例中，以下面的 `Counter` 组件为例，该组件使用 <xref:System.Timers.Timer?displayProperty=fullName> 定期更新计数并调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 来更新 UI。

`Pages/CounterWithTimerDisposal.razor`:

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

在上面的示例中：

* `OnTimerCallback` 必须调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，因为 Blazor 不知道回调中的 `currentCount` 更改。 `OnTimerCallback` 在 Blazor 管理的任何呈现流或事件通知之外运行。
* 组件实现 <xref:System.IDisposable>，当框架调用 `Dispose` 方法时，其中的 <xref:System.Timers.Timer> 将释放。 有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

同样，由于回调是在 Blazor 的同步上下文之外调用的，因此必须将逻辑包装在 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 中，以将其移到呈现器的同步上下文中。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能从呈现器的同步上下文调用，否则会引发异常。 这等效于封送到其他 UI 框架中的 UI 线程。

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>在由特定事件重新呈现的子树之外呈现组件

你的 UI 可能涉及将事件调度到一个组件，更改某种状态，并且需要重新呈现一个完全不同的组件，该组件不是接收事件的组件的后代。

应对这种情况的一种方法是，将状态管理类（可能作为 DI 服务）注入到多个组件中。 当一个组件在状态管理器上调用方法时，状态管理器可以引发 C# 事件，然后由独立组件接收该事件。

由于这些 C# 事件位于 Blazor 呈现管道之外，因此，请对要呈现的其他组件调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，以响应状态管理器的事件。

这与前面的 <xref:System.Timers.Timer?displayProperty=fullName> 案例（[上一部分](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中）类似。 由于执行调用堆栈通常保留在呈现器的同步上下文中，因此通常不需要 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>。 仅当逻辑转义同步上下文时才需要 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>，例如，对 <xref:System.Threading.Tasks.Task> 调用 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 或使用 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 等待 <xref:System.Threading.Tasks.Task>。
