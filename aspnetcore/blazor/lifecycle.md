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
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor 生命周期

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

Blazor 框架包括同步和异步生命周期方法。 重写生命周期方法，以便在组件初始化和呈现期间对组件执行其他操作。

## <a name="lifecycle-methods"></a>生命周期方法

### <a name="component-initialization-methods"></a>组件初始化方法

当组件从其父组件收到其初始参数后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>。 当组件执行异步操作时，请使用 `OnInitializedAsync`，并在操作完成时进行刷新。

对于同步操作，请重写 `OnInitialized`：

```csharp
protected override void OnInitialized()
{
    ...
}
```

若要执行异步操作，请重写 `OnInitializedAsync`，并对操作使用 `await` 关键字：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server apps 预[呈现其内容](xref:blazor/hosting-model-configuration#render-mode)调用 `OnInitializedAsync` **_两次_** ：

* 作为页的一部分，最初以静态方式呈现组件时。
* 第二次当浏览器与服务器建立连接时。

若要防止 `OnInitializedAsync` 中的开发人员代码运行两次，请参阅预[呈现后](#stateful-reconnection-after-prerendering)的有状态重新连接部分。

在预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。 在预呈现时，组件可能需要以不同的方式呈现。 有关详细信息，请参阅[检测应用程序何时预呈现](#detect-when-the-app-is-prerendering)部分。

### <a name="before-parameters-are-set"></a>设置参数之前

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> 设置由呈现树中的组件父级提供的参数：

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> 包含每次调用 `SetParametersAsync` 时的完整参数值集。

`SetParametersAsync` 的默认实现将每个属性的值设置为在 `ParameterView`中具有相应值的 `[Parameter]` 或 `[CascadingParameter]` 特性。 在 `ParameterView` 中没有相应值的参数将保持不变。

如果未调用 `base.SetParametersAync`，则自定义代码可以通过任何所需的方式解释传入参数值。 例如，不要求将传入参数分配给类的属性。

### <a name="after-parameters-are-set"></a>设置参数后

调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>：

* 当组件已初始化并收到其父组件的第一组参数时。
* 当父组件重新呈现并提供时：
  * 仅有至少一个参数发生更改的已知基元不可变类型。
  * 任何复杂类型的参数。 此框架无法知道复杂类型参数的值是否在内部转变，因此它将参数集视为已更改。

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>组件呈现后

当组件完成呈现后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>。 此时将填充元素和组件引用。 使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。

`OnAfterRenderAsync` 和 `OnAfterRender`的 `firstRender` 参数：

* 在第一次呈现组件实例时，设置为 `true`。
* 可用于确保仅执行一次初始化工作。

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
> 在 `OnAfterRenderAsync` 生命周期事件期间，必须立即进行渲染后的异步工作。
>
> 即使从 `OnAfterRenderAsync`中返回 <xref:System.Threading.Tasks.Task>，该任务完成后，框架也不会为组件计划进一步的呈现循环。 这是为了避免出现无限的呈现循环。 它与其他生命周期方法不同，后者将在返回的任务完成后计划更多的渲染循环。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

在*服务器上预呈现时不会调用*`OnAfterRender` 和 `OnAfterRenderAsync`。

### <a name="suppress-ui-refreshing"></a>禁止 UI 刷新

重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> 以取消 UI 刷新。 如果实现返回 `true`，则将刷新 UI：

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

每次呈现组件时，都将调用 `ShouldRender`。

即使 `ShouldRender` 被重写，组件也始终是最初呈现的。

## <a name="state-changes"></a>状态更改

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 通知组件状态已更改。 如果适用，调用 `StateHasChanged` 会导致组件重新呈现。

## <a name="handle-incomplete-async-actions-at-render"></a>在呈现时处理未完成的异步操作

在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 在执行生命周期方法时，对象可能 `null` 或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 `null`对象时呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。 `null``forecasts` 时，将向用户显示一条加载消息。 `OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。

*页面/FetchData* Blazor 服务器模板：

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>用 IDisposable 进行的组件处置

如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。 以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：

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
> 不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>。 `StateHasChanged` 可以作为撕裂渲染器的一部分被调用，因此不支持在该点请求 UI 更新。

## <a name="handle-errors"></a>处理错误

有关在执行生命周期方法期间处理错误的信息，请参阅 <xref:blazor/handle-errors#lifecycle-methods>。

## <a name="stateful-reconnection-after-prerendering"></a>预呈现后有状态重新连接

当 `RenderMode` `ServerPrerendered`时，在 Blazor Server 应用程序中，最初以页面的形式呈现组件。 一旦浏览器与服务器建立了连接，该组件将*再次*呈现，并且该组件现在是交互式的。 如果存在用于初始化组件的[OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods)生命周期方法，则将执行*两次*此方法：

* 如果组件预呈现静态，则为。
* 建立服务器连接之后。

这可能会导致在最终呈现组件时，UI 中显示的数据发生显著变化。

若要避免 Blazor 服务器应用中出现双重渲染方案：

* 传入一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重新启动后检索状态。
* 在预呈现期间使用标识符以保存组件状态。
* 在预呈现后使用标识符检索缓存状态。

下面的代码演示基于模板的 Blazor 服务器应用中的更新 `WeatherForecastService`，可避免双重呈现：

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

有关 `RenderMode`的详细信息，请参阅 <xref:blazor/hosting-model-configuration#render-mode>。

## <a name="detect-when-the-app-is-prerendering"></a>检测预呈现应用的时间

[!INCLUDE[](~/includes/blazor-prerendering.md)]
