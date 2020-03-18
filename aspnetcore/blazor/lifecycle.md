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
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor 生命周期

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Blazor 框架包括同步和异步生命周期方法。 替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。

## <a name="lifecycle-methods"></a>生命周期方法

### <a name="component-initialization-methods"></a>组件初始化方法

组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>。 在组件执行异步操作时使用 `OnInitializedAsync`，并应在操作完成后刷新。

对于同步操作，替代 `OnInitialized`：

```csharp
protected override void OnInitialized()
{
    ...
}
```

若要执行异步操作，请替代 `OnInitializedAsync` 并对该操作使用 `await` 关键字：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

[预呈现其内容](xref:blazor/hosting-model-configuration#render-mode)的 Blazor Server 应用调用 `OnInitializedAsync` **_两次_** ：

* 在组件最初作为页面的一部分静态呈现时调用一次。
* 在浏览器重新建立与服务器的连接时调用第二次。

为了防止 `OnInitializedAsync` 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。

在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行特定操作（例如调用 JavaScript）。 预呈现时，组件可能需要进行不同的呈现。 有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。

### <a name="before-parameters-are-set"></a>设置参数之前

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 在呈现树中设置组件的父组件提供的参数：

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

每次调用 `SetParametersAsync` 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含整个参数值集。

`SetParametersAsync` 的默认实现使用 `[Parameter]` 或 `[CascadingParameter]` 特性（在 `ParameterView` 中具有对应的值）设置每个属性的值。 在 `ParameterView` 中没有对应值的参数保持不变。

如果未调用 `base.SetParametersAync`，则自定义代码可使用任何需要的方式解释传入的参数值。 例如，不要求将传入参数分配给类的属性。

### <a name="after-parameters-are-set"></a>设置参数之后

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> 在以下情况下调用：

* 当组件被初始化并从其父组件收到其第一组参数时。
* 当父组件重新呈现并提供以下内容时：
  * 至少一个参数已更改的唯一已知基元不可变类型。
  * 任何复杂类型的参数。 框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 应用参数和属性值时，异步操作必须在 `OnParametersSetAsync` 生命周期事件期间发生。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>组件呈现之后

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> 在组件完成呈现后调用。 此时会填充元素和组件引用。 在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。

`OnAfterRenderAsync` 和 `OnAfterRender` 的 `firstRender` 参数：

* 在第一次呈现组件实例时设置为 `true`。
* 可用于确保初始化操作仅执行一次。

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
> 呈现后立即进行的异步操作必须在 `OnAfterRenderAsync` 生命周期事件期间发生。
>
> 即使从 `OnAfterRenderAsync` 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。 这是为了避免无限呈现循环。 它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

*在服务器上进行预呈现时*未调用 `OnAfterRender` 和 `OnAfterRenderAsync`。

### <a name="suppress-ui-refreshing"></a>禁止 UI 刷新

替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以禁止 UI 刷新。 如果实现返回 `true`，则刷新 UI：

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

每次呈现组件时都会调用 `ShouldRender`。

即使 `ShouldRender` 被替代，组件也始终在最初呈现。

## <a name="state-changes"></a>状态更改

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件其状态已更改。 如果适用，调用 `StateHasChanged` 会导致组件重新呈现。

## <a name="handle-incomplete-async-actions-at-render"></a>处理呈现时的不完整异步操作

在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 执行生命周期方法时，对象可能为 `null` 或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，替代 `OnInitializedAsync` 以异步接收预测数据 (`forecasts`)。 当 `forecasts` 为 `null` 时，将向用户显示加载消息。 `OnInitializedAsync` 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。

Blazor Server 模板中的 *Pages/FetchData.razor*：

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>使用 IDisposable 处置组件

如果组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时调用 [Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。 以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：

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
> 不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。 `StateHasChanged` 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。

## <a name="handle-errors"></a>处理错误

有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。

## <a name="stateful-reconnection-after-prerendering"></a>预呈现后的有状态重新连接

在 Blazor Server 应用中，当 `RenderMode` 为 `ServerPrerendered` 时，组件最初作为页面的一部分静态呈现。 浏览器重新建立与服务器的连接后，将*再次*呈现组件，并且该组件现在为交互式。 如果存在用于初始化组件的 [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) 生命周期方法，则该方法执行*两次*：

* 在静态预呈现组件时执行一次。
* 在建立服务器连接后执行一次。

在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。

若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：

* 传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。
* 在预呈现期间使用该标识符保存组件状态。
* 预呈现后使用该标识符检索缓存的状态。

以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现：

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

有关 `RenderMode` 的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。

## <a name="detect-when-the-app-is-prerendering"></a>检测应用何时预呈现

[!INCLUDE[](~/includes/blazor-prerendering.md)]
