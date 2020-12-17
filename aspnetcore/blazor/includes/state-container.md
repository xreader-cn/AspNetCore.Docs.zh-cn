<span data-ttu-id="0e976-101">嵌套组件通常使用链式绑定来绑定数据，如 <xref:blazor/components/data-binding> 中所述。</span><span class="sxs-lookup"><span data-stu-id="0e976-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="0e976-102">嵌套组件和非嵌套组件可以使用已注册的内存中状态容器来共享对数据的访问。</span><span class="sxs-lookup"><span data-stu-id="0e976-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="0e976-103">自定义状态容器类可以使用可分配的 <xref:System.Action>，来向应用不同部分中的组件通知状态更改。</span><span class="sxs-lookup"><span data-stu-id="0e976-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="0e976-104">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0e976-104">In the following example:</span></span>

* <span data-ttu-id="0e976-105">一对组件使用状态容器来跟踪属性。</span><span class="sxs-lookup"><span data-stu-id="0e976-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="0e976-106">示例的组件是嵌套的，但嵌套并非此方法生效的必要条件。</span><span class="sxs-lookup"><span data-stu-id="0e976-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="0e976-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e976-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="0e976-108">在 `Program.Main` (Blazor WebAssembly) 中：</span><span class="sxs-lookup"><span data-stu-id="0e976-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="0e976-109">在 `Startup.ConfigureServices` (Blazor Server) 中：</span><span class="sxs-lookup"><span data-stu-id="0e976-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="0e976-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="0e976-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="0e976-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="0e976-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
