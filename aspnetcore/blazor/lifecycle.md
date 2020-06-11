---
title: title:“ASP.NET Core Blazor 生命周期的作者：说明：“了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。”
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
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
ms.openlocfilehash: 9dcbb2ca21cc689063198e1ccc90583db4229183
ms.sourcegitcommit: ea98ab8b2f61aa09f2d74edbb62db339fa06f105
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83864576"
---
# <a name="aspnet-core-blazor-lifecycle"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

## <a name="lifecycle-methods"></a>'SignalR' uid:

### <a name="component-initialization-methods"></a>ASP.NET Core Blazor 生命周期

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27) Blazor 框架包括同步和异步生命周期方法。

替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。

```csharp
protected override void OnInitialized()
{
    ...
}
```

生命周期方法

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

组件初始化方法

* 组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>。
* 在组件执行异步操作时使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>，并应在操作完成后刷新。

对于同步操作，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>：

若要执行异步操作，请替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 并对该操作使用 [await](/dotnet/csharp/language-reference/operators/await) 运算符： [预呈现其内容](xref:blazor/hosting-model-configuration#render-mode)的 Blazor Server 应用调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_两次_**： 在组件最初作为页面的一部分静态呈现时调用一次。

在浏览器重新建立与服务器的连接时调用第二次。 为了防止 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。

### <a name="before-parameters-are-set"></a>在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行特定操作（例如调用 JavaScript）。

预呈现时，组件可能需要进行不同的呈现。

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。

如果设置有事件处理程序，处置时会将其解除挂接。 有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。

设置参数之前 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在呈现树中设置组件的父组件提供的参数：

每次调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含整个参数值集。 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 的默认实现使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 特性（在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中具有对应的值）设置每个属性的值。

### <a name="after-parameters-are-set"></a>在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中没有对应值的参数保持不变。

如果未调用 [base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)，则自定义代码可使用任何需要的方式解释传入的参数值。

* 例如，不要求将传入参数分配给类的属性。
* 如果设置有事件处理程序，处置时会将其解除挂接。
  * 有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。
  * 设置参数之后 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 在以下情况下调用：

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 当组件被初始化并从其父组件收到其第一组参数时。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

当父组件重新呈现并提供以下内容时： 至少一个参数已更改的唯一已知基元不可变类型。

### <a name="after-component-render"></a>任何复杂类型的参数。

框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。 应用参数和属性值时，异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命周期事件期间发生。 如果设置有事件处理程序，处置时会将其解除挂接。

有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。

* 组件呈现之后
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在组件完成呈现后调用。

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
> 此时会填充元素和组件引用。
>
> 在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 的 `firstRender` 参数： 在第一次呈现组件实例时设置为 `true`。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

可用于确保初始化操作仅执行一次。

呈现后立即进行的异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命周期事件期间发生。 即使从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。

### <a name="suppress-ui-refreshing"></a>这是为了避免无限呈现循环。

它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。 *在服务器上进行预呈现时*未调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

如果设置有事件处理程序，处置时会将其解除挂接。

有关详细信息，请参阅[使用 IDisposable 处置组件](#component-disposal-with-idisposable)部分。

禁止 UI 刷新

## <a name="state-changes"></a>替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以禁止 UI 刷新。

如果实现返回 `true`，则刷新 UI： 每次呈现组件时都会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>。

## <a name="handle-incomplete-async-actions-at-render"></a>即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被替代，组件也始终在最初呈现。

有关详细信息，请参阅 <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>。 状态更改 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。 如果适用，调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会导致组件重新呈现。

处理呈现时的不完整异步操作 在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 执行生命周期方法时，对象可能为 `null` 或未完全填充数据。

提供呈现逻辑以确认对象已初始化。

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 以异步接收预测数据 (`forecasts`)。 当 `forecasts` 为 `null` 时，将向用户显示加载消息。

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
> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。 Blazor Server 模板中的 *Pages/FetchData.razor*：

使用 IDisposable 处置组件 如果组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时调用 [Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

* 以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* 不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。

取消订阅 .NET 事件中的事件处理程序。

## <a name="stateful-reconnection-after-prerendering"></a>下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何解除挂接 `Dispose` 方法中的事件处理程序：

专用字段和 Lambda 方法 专用方法 处理错误

* 有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。
* 预呈现后的有状态重新连接

在 Blazor Server 应用中，当 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> 时，组件最初作为页面的一部分静态呈现。

浏览器重新建立与服务器的连接后，将*再次*呈现组件，并且该组件现在为交互式。

* 如果存在用于初始化组件的 [OnInitialized{Async}](#component-initialization-methods) 生命周期方法，则该方法执行*两次*：
* 在静态预呈现组件时执行一次。
* 在建立服务器连接后执行一次。

在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。

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

若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：

## <a name="detect-when-the-app-is-prerendering"></a>传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>在预呈现期间使用该标识符保存组件状态。

预呈现后使用该标识符检索缓存的状态。 以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现： 有关 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。

检测应用何时预呈现

* 可取消的后台工作
* 组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。
* 在几种情况下，最好停止后台工作以节省系统资源。
* 例如，当用户离开组件时，后台异步操作不会自动停止。
* 后台工作项可能需要取消的其他原因包括：

正在执行的后台任务由错误的输入数据或处理参数启动。

* 正在执行的一组后台工作项必须替换为一组新的工作项。
* 必须更改当前正在执行的任务的优先级。
* 必须关闭应用才能将其重新部署到服务器。

服务器资源受到限制，需要重新计划后台工作项。

* 要在组件中实现可取消的后台工作模式：
* 使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。

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
