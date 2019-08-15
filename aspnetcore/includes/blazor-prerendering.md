<span data-ttu-id="d622e-101">预呈现 Blazor 服务器端应用程序时, 由于尚未建立与浏览器的连接, 因此无法执行某些操作 (如调用 JavaScript)。</span><span class="sxs-lookup"><span data-stu-id="d622e-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="d622e-102">在预呈现时, 组件可能需要以不同的方式呈现。</span><span class="sxs-lookup"><span data-stu-id="d622e-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="d622e-103">若要在建立与浏览器的连接后延迟 JavaScript 互操作调用, 可以使用`OnAfterRenderAsync`组件生命周期事件。</span><span class="sxs-lookup"><span data-stu-id="d622e-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="d622e-104">仅在完全呈现应用并建立客户端连接后, 才调用此事件。</span><span class="sxs-lookup"><span data-stu-id="d622e-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" @ref:suppressField value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="d622e-105">以下组件演示了如何以与预呈现兼容的方式将 JavaScript 互操作作为组件的初始化逻辑的一部分使用。</span><span class="sxs-lookup"><span data-stu-id="d622e-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="d622e-106">组件显示可以从内部`OnAfterRenderAsync`触发呈现更新。</span><span class="sxs-lookup"><span data-stu-id="d622e-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="d622e-107">开发人员必须避免在此方案中创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="d622e-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="d622e-108">在中, 调用`ElementRef` , 仅在任何`OnAfterRenderAsync`早期的生命周期方法中使用, 因为在呈现组件之前没有 JavaScript 元素。 `JSRuntime.InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="d622e-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="d622e-109">`StateHasChanged`调用以诸如此类具有从 JavaScript 互操作调用获取的新状态的组件。</span><span class="sxs-lookup"><span data-stu-id="d622e-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="d622e-110">由于仅在为`StateHasChanged` `infoFromJs` `null`时调用, 因此代码不会创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="d622e-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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
    <input id="val-set-by-interop" @ref="myElem" @ref:suppressField />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync()
    {
        // TEMPORARY: Currently we need this guard to avoid making the interop
        // call during prerendering. Soon this will be unnecessary because we
        // will change OnAfterRenderAsync so that it won't run during the
        // prerendering phase.
        if (!ComponentContext.IsConnected)
        {
            return;
        }

        if (infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="d622e-111">若要根据应用当前是否为预呈现内容, 有条件地呈现不同的`IsConnected`内容, 请`IComponentContext`对服务使用属性。</span><span class="sxs-lookup"><span data-stu-id="d622e-111">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="d622e-112">在运行服务器端时, `IsConnected`仅当`true`有与客户端的活动连接时才返回。</span><span class="sxs-lookup"><span data-stu-id="d622e-112">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="d622e-113">当客户端`true`运行时, 它始终返回。</span><span class="sxs-lookup"><span data-stu-id="d622e-113">It always returns `true` when running client-side.</span></span>

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
