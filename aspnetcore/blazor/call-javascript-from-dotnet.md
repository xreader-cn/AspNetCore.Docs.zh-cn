---
title: 在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数
author: guardrex
description: 了解如何在 Blazor 应用中从 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: da4ce8a2610fc07d22153f66831d693ae66e0fe5
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424147"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="43a0b-103">在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="43a0b-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="43a0b-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43a0b-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43a0b-105">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="43a0b-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="43a0b-106">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="43a0b-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="43a0b-107">本文介绍如何从 .NET 调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="43a0b-108">有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。</span><span class="sxs-lookup"><span data-stu-id="43a0b-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="43a0b-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="43a0b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="43a0b-110">若要从 .NET 调入 JavaScript，请使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="43a0b-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="43a0b-111">若要发出 JS 互操作调用，请在组件中注入 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="43a0b-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="43a0b-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 需要使用你要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="43a0b-113">函数标识符相对于全局范围 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="43a0b-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="43a0b-114">如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="43a0b-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="43a0b-115">无需在调用函数之前进行注册。</span><span class="sxs-lookup"><span data-stu-id="43a0b-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="43a0b-116">返回类型 `T` 也必须可进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="43a0b-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="43a0b-117">`T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="43a0b-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="43a0b-118">返回 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="43a0b-119">`InvokeAsync` 会将 Promise 解包并返回 Promise 所等待的值。</span><span class="sxs-lookup"><span data-stu-id="43a0b-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="43a0b-120">对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="43a0b-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="43a0b-121">在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="43a0b-122">有关详细信息，请参阅[检测 Blazor Server 应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="43a0b-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="43a0b-123">下面的示例基于 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="43a0b-124">此示例展示了如何通过 C# 方法调用 JavaScript 函数，以将要求从开发人员代码卸载到现有 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="43a0b-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="43a0b-125">JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="43a0b-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="43a0b-126">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="43a0b-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="43a0b-127">JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (`.js`) 加载：</span><span class="sxs-lookup"><span data-stu-id="43a0b-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="43a0b-128">以下组件：</span><span class="sxs-lookup"><span data-stu-id="43a0b-128">The following component:</span></span>

* <span data-ttu-id="43a0b-129">在选择了组件按钮（“`Convert Array`”）后，使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="43a0b-130">调用 JavaScript 函数之后，传递的数组会转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="43a0b-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="43a0b-131">该字符串会返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="43a0b-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="43a0b-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="43a0b-132">IJSRuntime</span></span>

<span data-ttu-id="43a0b-133">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象，请采用以下任何方法：</span><span class="sxs-lookup"><span data-stu-id="43a0b-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="43a0b-134">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入 Razor 组件 (`.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="43a0b-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="43a0b-135">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="43a0b-136">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="43a0b-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="43a0b-137">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入类 (`.cs`)：</span><span class="sxs-lookup"><span data-stu-id="43a0b-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="43a0b-138">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="43a0b-139">该函数通过 `JSRuntime.InvokeAsync` 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="43a0b-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="43a0b-140">对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：</span><span class="sxs-lookup"><span data-stu-id="43a0b-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="43a0b-141">在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="43a0b-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="43a0b-142">`showPrompt`：生成一个提示，用于接受用户输入（用户名），并将用户名返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="43a0b-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="43a0b-143">`displayWelcome`：将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="43a0b-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="43a0b-144">`wwwroot/exampleJsInterop.js`：</span><span class="sxs-lookup"><span data-stu-id="43a0b-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="43a0b-145">将引用 JavaScript 文件的 `<script>` 标记置于 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中。</span><span class="sxs-lookup"><span data-stu-id="43a0b-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="43a0b-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="43a0b-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="43a0b-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="43a0b-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="43a0b-148">请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="43a0b-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="43a0b-149">.NET 方法通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 与 `exampleJsInterop.js` 文件中的 JavaScript 函数进行互操作。</span><span class="sxs-lookup"><span data-stu-id="43a0b-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="43a0b-150"><xref:Microsoft.JSInterop.IJSRuntime> 抽象是异步的，以便可以实现 Blazor Server 方案。</span><span class="sxs-lookup"><span data-stu-id="43a0b-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="43a0b-151">如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 并改为调用 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>。</span><span class="sxs-lookup"><span data-stu-id="43a0b-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="43a0b-152">建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="43a0b-153">若要在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离，请参阅 [BlazorJavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)部分。</span><span class="sxs-lookup"><span data-stu-id="43a0b-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="43a0b-154">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="43a0b-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="43a0b-155">该组件：</span><span class="sxs-lookup"><span data-stu-id="43a0b-155">The component:</span></span>

* <span data-ttu-id="43a0b-156">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="43a0b-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="43a0b-157">将文本返回给组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="43a0b-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="43a0b-158">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="43a0b-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="43a0b-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="43a0b-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="43a0b-160">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="43a0b-161">通过选择组件的“`Trigger JavaScript Prompt`”按钮来执行 `TriggerJsPrompt` 时，则会调用在 `wwwroot/exampleJsInterop.js` 文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="43a0b-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="43a0b-162">`showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="43a0b-163">组件将用户的名称存储在本地变量 `name` 中。</span><span class="sxs-lookup"><span data-stu-id="43a0b-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="43a0b-164">存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="43a0b-165">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="43a0b-165">Call a void JavaScript function</span></span>

<span data-ttu-id="43a0b-166">返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="43a0b-167">检测 Blazor Server 应用进行预呈现的时间</span><span class="sxs-lookup"><span data-stu-id="43a0b-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="43a0b-168">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="43a0b-168">Capture references to elements</span></span>

<span data-ttu-id="43a0b-169">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="43a0b-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="43a0b-170">例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="43a0b-171">使用以下方法在组件中捕获对 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="43a0b-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="43a0b-172">向 HTML 元素添加 `@ref` 属性。</span><span class="sxs-lookup"><span data-stu-id="43a0b-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="43a0b-173">定义一个类型为 <xref:Microsoft.AspNetCore.Components.ElementReference> 字段，其名称与 `@ref` 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="43a0b-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="43a0b-174">以下示例演示如何捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="43a0b-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="43a0b-175">只使用元素引用改变不与 Blazor 交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="43a0b-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="43a0b-176">当第三方 API 向元素提供内容时，此方案十分有用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="43a0b-177">由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。</span><span class="sxs-lookup"><span data-stu-id="43a0b-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="43a0b-178">在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：</span><span class="sxs-lookup"><span data-stu-id="43a0b-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="43a0b-179">如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="43a0b-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="43a0b-180">就 .NET 代码而言，<xref:Microsoft.AspNetCore.Components.ElementReference> 是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="43a0b-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="43a0b-181">可以对 <xref:Microsoft.AspNetCore.Components.ElementReference> 执行的唯一操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="43a0b-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="43a0b-182">执行此操作时，JavaScript 端代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="43a0b-183">例如，以下代码定义一个 .NET 扩展方法，通过该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="43a0b-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="43a0b-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="43a0b-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="43a0b-185">若要调用不返回值的 JavaScript 函数，请使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="43a0b-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="43a0b-186">下面的代码通过使用捕获的 <xref:Microsoft.AspNetCore.Components.ElementReference> 调用前面的 JavaScript 函数，在用户名输入上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="43a0b-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="43a0b-187">若要使用扩展方法，请创建接收 <xref:Microsoft.JSInterop.IJSRuntime> 实例的静态扩展方法：</span><span class="sxs-lookup"><span data-stu-id="43a0b-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="43a0b-188">`Focus` 方法在对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="43a0b-189">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `Focus` 方法：</span><span class="sxs-lookup"><span data-stu-id="43a0b-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="43a0b-190">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="43a0b-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="43a0b-191">如果将未填充的 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="43a0b-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="43a0b-192">若要在组件完成呈现之后操作元素引用（用于对元素设置初始焦点），请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 组件生命周期方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="43a0b-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="43a0b-193">使用泛型类型并返回值时，请使用 <xref:System.Threading.Tasks.ValueTask%601>：</span><span class="sxs-lookup"><span data-stu-id="43a0b-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="43a0b-194">`GenericMethod` 在具有类型的对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="43a0b-195">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="43a0b-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="43a0b-196">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="43a0b-196">Reference elements across components</span></span>

<span data-ttu-id="43a0b-197"><xref:Microsoft.AspNetCore.Components.ElementReference> 仅保证在组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="43a0b-198">若要使父组件可以向其他组件提供元素引用，父组件可以：</span><span class="sxs-lookup"><span data-stu-id="43a0b-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="43a0b-199">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="43a0b-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="43a0b-200">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="43a0b-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="43a0b-201">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="43a0b-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="43a0b-202">下面的 Blazor WebAssembly 示例阐释了这种方法。</span><span class="sxs-lookup"><span data-stu-id="43a0b-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="43a0b-203">在 `wwwroot/index.html` 的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="43a0b-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="43a0b-204">在 `wwwroot/index.html` 的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="43a0b-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="43a0b-205">`Pages/Index.razor`（父组件）：</span><span class="sxs-lookup"><span data-stu-id="43a0b-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="43a0b-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="43a0b-206">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="43a0b-207">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="43a0b-208">`Shared/SurveyPrompt.razor`（子组件）：</span><span class="sxs-lookup"><span data-stu-id="43a0b-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="43a0b-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="43a0b-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="43a0b-210">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="43a0b-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="43a0b-211">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="43a0b-211">Harden JS interop calls</span></span>

<span data-ttu-id="43a0b-212">JS 互操作可能会由于网络错误而失败，因此应视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="43a0b-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="43a0b-213">默认情况下，Blazor Server 应用会在一分钟后，使服务器上的 JS 互操作调用超时。</span><span class="sxs-lookup"><span data-stu-id="43a0b-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="43a0b-214">如果应用可以容忍更激进的超时，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="43a0b-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="43a0b-215">在 `Startup.ConfigureServices` 中全局指定超时：</span><span class="sxs-lookup"><span data-stu-id="43a0b-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="43a0b-216">对于组件代码中的每个调用，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="43a0b-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="43a0b-217">有关资源耗尽的详细信息，请参阅 <xref:blazor/security/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="43a0b-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="43a0b-218">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="43a0b-218">Avoid circular object references</span></span>

<span data-ttu-id="43a0b-219">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="43a0b-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="43a0b-220">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-220">.NET method calls.</span></span>
* <span data-ttu-id="43a0b-221">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="43a0b-222">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="43a0b-222">For more information, see the following issues:</span></span>

* [<span data-ttu-id="43a0b-223">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="43a0b-223">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="43a0b-224">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="43a0b-224">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="43a0b-225">Blazor JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="43a0b-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="43a0b-226">Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="43a0b-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="43a0b-227">JavaScript 隔离具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="43a0b-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="43a0b-228">导入的 JavaScript 不再污染全局命名空间。</span><span class="sxs-lookup"><span data-stu-id="43a0b-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="43a0b-229">库和组件的使用者不需要导入相关的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="43a0b-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="43a0b-230">例如，以下 JavaScript 模块导出用于显示浏览器提示的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="43a0b-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="43a0b-231">将前面的 JavaScript 模块作为静态 Web 资产 (`wwwroot/exampleJsInterop.js`) 添加到 .NET 库，然后使用 <xref:Microsoft.JSInterop.IJSRuntime> 服务将该模块导入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="43a0b-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="43a0b-232">以下示例将服务作为 `jsRuntime`（未显示）注入：</span><span class="sxs-lookup"><span data-stu-id="43a0b-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="43a0b-233">上例中的 `import` 标识符是专门用于导入 JavaScript 模块的特殊标识符。</span><span class="sxs-lookup"><span data-stu-id="43a0b-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="43a0b-234">使用模块的稳定静态 Web 资产路径 `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 指定模块。</span><span class="sxs-lookup"><span data-stu-id="43a0b-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="43a0b-235">占位符 `{LIBRARY NAME}` 是库的名称。</span><span class="sxs-lookup"><span data-stu-id="43a0b-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="43a0b-236">占位符 `{PATH UNDER WWWROOT}` 是 `wwwroot` 下脚本的路径。</span><span class="sxs-lookup"><span data-stu-id="43a0b-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="43a0b-237"><xref:Microsoft.JSInterop.IJSRuntime> 将模块作为 `JSObjectReference` 导入，它表示对 .NET 代码中 JavaScript 对象的引用。</span><span class="sxs-lookup"><span data-stu-id="43a0b-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="43a0b-238">使用 `JSObjectReference` 调用从模块导出的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="43a0b-238">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="43a0b-239">其他资源</span><span class="sxs-lookup"><span data-stu-id="43a0b-239">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="43a0b-240">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="43a0b-240">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="43a0b-241">在 Blazor Server 应用中执行大型数据传输</span><span class="sxs-lookup"><span data-stu-id="43a0b-241">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
