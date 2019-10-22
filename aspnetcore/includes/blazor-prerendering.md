预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。 在预呈现时，组件可能需要以不同的方式呈现。

若要在建立与浏览器的连接之前延迟 JavaScript 互操作调用，你可以使用 `OnAfterRenderAsync` 组件生命周期事件。 仅在完全呈现应用并建立客户端连接后，才调用此事件。

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

对于前面的示例代码，请在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素内提供 `setElementText` JavaScript 函数。 使用 `IJSRuntime.InvokeVoidAsync` 调用函数，并且不返回值：

```html
<!--  -->
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> 前面的示例仅修改了文档对象模型（DOM），以便仅用于演示目的。 大多数情况下不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。

以下组件演示了如何以与预呈现兼容的方式将 JavaScript 互操作作为组件的初始化逻辑的一部分使用。 该组件显示可以从 `OnAfterRenderAsync` 内触发呈现更新。 开发人员必须避免在此方案中创建无限循环。

在调用 `JSRuntime.InvokeAsync` 的情况下，`ElementRef` 仅用于 `OnAfterRenderAsync`，而不是在任何早期的生命周期方法中，因为在呈现组件之前，不会有 JavaScript 元素。

调用 `StateHasChanged`，以将组件与从 JavaScript 互操作调用获取的新状态诸如此类。 此代码不会创建无限循环，因为仅当 `infoFromJs` `null` 时才会调用 `StateHasChanged`。

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

对于前面的示例代码，请在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素内提供 `setElementText` JavaScript 函数。 使用 `IJSRuntime.InvokeAsync` 调用函数并返回值：

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> 前面的示例仅修改了文档对象模型（DOM），以便仅用于演示目的。 大多数情况下不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。
