<span data-ttu-id="de89e-101">预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。</span><span class="sxs-lookup"><span data-stu-id="de89e-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="de89e-102">在预呈现时，组件可能需要以不同的方式呈现。</span><span class="sxs-lookup"><span data-stu-id="de89e-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="de89e-103">若要在建立与浏览器的连接之前延迟 JavaScript 互操作调用，你可以使用 `OnAfterRenderAsync` 组件生命周期事件。</span><span class="sxs-lookup"><span data-stu-id="de89e-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="de89e-104">仅在完全呈现应用并建立客户端连接后，才调用此事件。</span><span class="sxs-lookup"><span data-stu-id="de89e-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="de89e-105">以下组件演示了如何以与预呈现兼容的方式将 JavaScript 互操作作为组件的初始化逻辑的一部分使用。</span><span class="sxs-lookup"><span data-stu-id="de89e-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="de89e-106">该组件显示可以从 `OnAfterRenderAsync` 内触发呈现更新。</span><span class="sxs-lookup"><span data-stu-id="de89e-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="de89e-107">开发人员必须避免在此方案中创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="de89e-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="de89e-108">在调用 `JSRuntime.InvokeAsync` 的情况下，`ElementRef` 仅用于 `OnAfterRenderAsync`，而不是在任何早期的生命周期方法中，因为在呈现组件之前，不会有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="de89e-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="de89e-109">调用 `StateHasChanged`，以将组件与从 JavaScript 互操作调用获取的新状态诸如此类。</span><span class="sxs-lookup"><span data-stu-id="de89e-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="de89e-110">此代码不会创建无限循环，因为仅当 `infoFromJs` `null` 时才会调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="de89e-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
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
