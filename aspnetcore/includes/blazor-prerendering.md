<span data-ttu-id="3fb25-101">预呈现 Blazor 服务器应用时，某些操作（如调用 JavaScript）不可能，因为尚未建立与浏览器的连接。</span><span class="sxs-lookup"><span data-stu-id="3fb25-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="3fb25-102">在预呈现时，组件可能需要以不同的方式呈现。</span><span class="sxs-lookup"><span data-stu-id="3fb25-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="3fb25-103">若要在建立与浏览器的连接之前延迟 JavaScript 互操作调用，你可以使用 `OnAfterRenderAsync` 组件生命周期事件。</span><span class="sxs-lookup"><span data-stu-id="3fb25-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="3fb25-104">仅在完全呈现应用并建立客户端连接后，才调用此事件。</span><span class="sxs-lookup"><span data-stu-id="3fb25-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="3fb25-105">对于前面的示例代码，请在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素内提供 `setElementText` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="3fb25-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="3fb25-106">使用 `IJSRuntime.InvokeVoidAsync` 调用函数，并且不返回值：</span><span class="sxs-lookup"><span data-stu-id="3fb25-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="3fb25-107">前面的示例仅修改了文档对象模型（DOM），以便仅用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="3fb25-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="3fb25-108">大多数情况下不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="3fb25-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="3fb25-109">以下组件演示了如何以与预呈现兼容的方式将 JavaScript 互操作作为组件的初始化逻辑的一部分使用。</span><span class="sxs-lookup"><span data-stu-id="3fb25-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="3fb25-110">该组件显示可以从 `OnAfterRenderAsync` 内触发呈现更新。</span><span class="sxs-lookup"><span data-stu-id="3fb25-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="3fb25-111">开发人员必须避免在此方案中创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="3fb25-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="3fb25-112">在调用 `JSRuntime.InvokeAsync` 的情况下，`ElementRef` 仅用于 `OnAfterRenderAsync`，而不是在任何早期的生命周期方法中，因为在呈现组件之前，不会有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="3fb25-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="3fb25-113">调用 `StateHasChanged`，以将组件与从 JavaScript 互操作调用获取的新状态诸如此类。</span><span class="sxs-lookup"><span data-stu-id="3fb25-113">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="3fb25-114">此代码不会创建无限循环，因为仅当 `infoFromJs` `null` 时才会调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="3fb25-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="3fb25-115">对于前面的示例代码，请在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素内提供 `setElementText` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="3fb25-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="3fb25-116">使用 `IJSRuntime.InvokeAsync` 调用函数并返回值：</span><span class="sxs-lookup"><span data-stu-id="3fb25-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="3fb25-117">前面的示例仅修改了文档对象模型（DOM），以便仅用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="3fb25-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="3fb25-118">大多数情况下不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="3fb25-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
