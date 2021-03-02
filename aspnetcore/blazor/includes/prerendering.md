---
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
ms.openlocfilehash: c152524e0acd3803bd3b8078f667cce01180e25d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551656"
---
<span data-ttu-id="dc301-101">在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行调用 JavaScript 等特定操作。</span><span class="sxs-lookup"><span data-stu-id="dc301-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="dc301-102">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="dc301-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="dc301-103">要将 JavaScript 互操作调用延迟到与浏览器建立连接之后，可使用 [OnAfterRenderAsync 组件生命周期事件](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="dc301-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="dc301-104">仅在完成呈现应用并与客户端建立连接后，才会调用此事件。</span><span class="sxs-lookup"><span data-stu-id="dc301-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="dc301-105">对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="dc301-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="dc301-106">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="dc301-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="dc301-107">上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。</span><span class="sxs-lookup"><span data-stu-id="dc301-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="dc301-108">大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="dc301-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="dc301-109">以下组件展示了如何以一种与预呈现兼容的方式将 JavaScript 互操作用作组件初始化逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="dc301-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="dc301-110">该组件显示可从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 内部触发呈现更新。</span><span class="sxs-lookup"><span data-stu-id="dc301-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="dc301-111">开发人员必须避免在此场景中创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="dc301-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="dc301-112">如果调用 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType>，则 `ElementRef` 仅在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 中使用，而不在任何更早的生命周期方法中使用，因为呈现组件后才会有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="dc301-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="dc301-113">通过调用 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)，可使用从 JavaScript 互操作调用中获取的新状态重新呈现组件（有关详细信息，请参阅 <xref:blazor/components/rendering>）。</span><span class="sxs-lookup"><span data-stu-id="dc301-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="dc301-114">此代码不会创建无限循环，因为仅在 `infoFromJs` 为 `null` 时才调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="dc301-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="dc301-115">对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="dc301-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="dc301-116">该函数通过 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="dc301-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="dc301-117">上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。</span><span class="sxs-lookup"><span data-stu-id="dc301-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="dc301-118">大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="dc301-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
