预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。 在预呈现时，组件可能需要以不同的方式呈现。

若要在建立与浏览器的连接后延迟 JavaScript 互操作调用，可以使用`OnAfterRenderAsync`组件生命周期事件。 仅在完全呈现应用并建立客户端连接后，才调用此事件。

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeAsync<object>(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

以下组件演示了如何以与预呈现兼容的方式将 JavaScript 互操作作为组件的初始化逻辑的一部分使用。 组件显示可以从内部`OnAfterRenderAsync`触发呈现更新。 开发人员必须避免在此方案中创建无限循环。

在中，调用`ElementRef` ，仅在任何`OnAfterRenderAsync`早期的生命周期方法中使用，因为在呈现组件之前没有 JavaScript 元素。 `JSRuntime.InvokeAsync`

`StateHasChanged`调用以诸如此类具有从 JavaScript 互操作调用获取的新状态的组件。 由于仅在为`StateHasChanged` `infoFromJs` `null`时调用，因此代码不会创建无限循环。

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

若要根据应用当前是否为预呈现内容，有条件地呈现不同的`IsConnected`内容，请`IComponentContext`对服务使用属性。 对于 Blazor 服务器应用， `IsConnected`仅当`true`有与客户端的活动连接时才返回。 它始终在`true` Blazor WebAssembly apps 中返回。

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```
