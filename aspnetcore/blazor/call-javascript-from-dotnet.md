---
title: 在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数
author: guardrex
description: 了解如何在 Blazor 应用中从 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5b28ad594567e7bfc87e15eed419bea520125654
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280308"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="41421-103">在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="41421-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="41421-104">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="41421-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="41421-105">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="41421-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="41421-106">本文介绍如何从 .NET 调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-106">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="41421-107">有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。</span><span class="sxs-lookup"><span data-stu-id="41421-107">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="41421-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="41421-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="41421-109">将 JS 文件（`<script>` 标记）添加到 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中的 `</body>` 结束标记前。</span><span class="sxs-lookup"><span data-stu-id="41421-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="41421-110">确保在 Blazor framework JS 文件之前包含带有 JS 互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="41421-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="41421-111">若要从 .NET 调入 JavaScript，请使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="41421-111">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="41421-112">若要发出 JS 互操作调用，请在组件中注入 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="41421-112">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="41421-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 需要使用你要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="41421-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="41421-114">函数标识符相对于全局范围 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="41421-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="41421-115">如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="41421-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="41421-116">无需在调用函数之前进行注册。</span><span class="sxs-lookup"><span data-stu-id="41421-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="41421-117">返回类型 `T` 也必须可进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="41421-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="41421-118">`T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="41421-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="41421-119">返回 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 调用。</span><span class="sxs-lookup"><span data-stu-id="41421-119">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="41421-120">`InvokeAsync` 会将 Promise 解包并返回 Promise 所等待的值。</span><span class="sxs-lookup"><span data-stu-id="41421-120">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="41421-121">对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="41421-121">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="41421-122">在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="41421-122">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="41421-123">有关详细信息，请参阅[检测 Blazor Server 应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="41421-123">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="41421-124">下面的示例基于 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="41421-124">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="41421-125">此示例展示了如何通过 C# 方法调用 JavaScript 函数，以将要求从开发人员代码卸载到现有 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="41421-125">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="41421-126">JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="41421-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="41421-127">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="41421-127">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="41421-128">JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (`.js`) 加载：</span><span class="sxs-lookup"><span data-stu-id="41421-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="41421-129">以下组件：</span><span class="sxs-lookup"><span data-stu-id="41421-129">The following component:</span></span>

* <span data-ttu-id="41421-130">在选择了组件按钮（“`Convert Array`”）后，使用 `JS` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-130">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="41421-131">调用 JavaScript 函数之后，传递的数组会转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="41421-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="41421-132">该字符串会返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="41421-132">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="41421-133">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="41421-133">IJSRuntime</span></span>

<span data-ttu-id="41421-134">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象，请采用以下任何方法：</span><span class="sxs-lookup"><span data-stu-id="41421-134">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="41421-135">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入 Razor 组件 (`.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="41421-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="41421-136">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="41421-137">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="41421-137">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="41421-138">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入类 (`.cs`)：</span><span class="sxs-lookup"><span data-stu-id="41421-138">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="41421-139">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-139">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="41421-140">该函数通过 `JS.InvokeAsync` 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="41421-140">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="41421-141">对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：</span><span class="sxs-lookup"><span data-stu-id="41421-141">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="41421-142">在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="41421-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="41421-143">`showPrompt`：生成一个提示，用于接受用户输入（用户名），并将用户名返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="41421-143">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="41421-144">`displayWelcome`：将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="41421-144">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="41421-145">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="41421-145">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="41421-146">将引用 JavaScript 文件的 `<script>` 标记置于 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中。</span><span class="sxs-lookup"><span data-stu-id="41421-146">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="41421-147">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="41421-147">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=23)]

<span data-ttu-id="41421-148">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="41421-148">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="41421-149">请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="41421-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="41421-150">.NET 方法通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 与 `exampleJsInterop.js` 文件中的 JavaScript 函数进行互操作。</span><span class="sxs-lookup"><span data-stu-id="41421-150">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="41421-151"><xref:Microsoft.JSInterop.IJSRuntime> 抽象是异步的，以便可以实现 Blazor Server 方案。</span><span class="sxs-lookup"><span data-stu-id="41421-151">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="41421-152">如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 并改为调用 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>。</span><span class="sxs-lookup"><span data-stu-id="41421-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="41421-153">建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="41421-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="41421-154">若要在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离，请参阅 [BlazorJavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)部分。</span><span class="sxs-lookup"><span data-stu-id="41421-154">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="41421-155">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="41421-155">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="41421-156">该组件：</span><span class="sxs-lookup"><span data-stu-id="41421-156">The component:</span></span>

* <span data-ttu-id="41421-157">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="41421-157">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="41421-158">将文本返回给组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="41421-158">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="41421-159">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="41421-159">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="41421-160">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="41421-160">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="41421-161">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="41421-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="41421-162">通过选择组件的“`Trigger JavaScript Prompt`”按钮来执行 `TriggerJsPrompt` 时，则会调用在 `wwwroot/exampleJsInterop.js` 文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-162">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="41421-163">`showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="41421-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="41421-164">组件将用户的名称存储在本地变量 `name` 中。</span><span class="sxs-lookup"><span data-stu-id="41421-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="41421-165">存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。</span><span class="sxs-lookup"><span data-stu-id="41421-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="41421-166">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="41421-166">Call a void JavaScript function</span></span>

<span data-ttu-id="41421-167">在以下场景中使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="41421-167">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="41421-168">返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="41421-168">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="41421-169">如果 .NET 不需要读取 JavaScript 调用的结果。</span><span class="sxs-lookup"><span data-stu-id="41421-169">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="41421-170">检测 Blazor Server 应用进行预呈现的时间</span><span class="sxs-lookup"><span data-stu-id="41421-170">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="41421-171">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="41421-171">Capture references to elements</span></span>

<span data-ttu-id="41421-172">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="41421-172">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="41421-173">例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。</span><span class="sxs-lookup"><span data-stu-id="41421-173">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="41421-174">使用以下方法在组件中捕获对 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="41421-174">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="41421-175">向 HTML 元素添加 `@ref` 属性。</span><span class="sxs-lookup"><span data-stu-id="41421-175">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="41421-176">定义一个类型为 <xref:Microsoft.AspNetCore.Components.ElementReference> 字段，其名称与 `@ref` 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="41421-176">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="41421-177">以下示例演示如何捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="41421-177">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="41421-178">只使用元素引用改变不与 Blazor 交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="41421-178">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="41421-179">当第三方 API 向元素提供内容时，此方案十分有用。</span><span class="sxs-lookup"><span data-stu-id="41421-179">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="41421-180">由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。</span><span class="sxs-lookup"><span data-stu-id="41421-180">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="41421-181">在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：</span><span class="sxs-lookup"><span data-stu-id="41421-181">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="41421-182">如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="41421-182">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="41421-183">通过 JS 互操作将 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="41421-183">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="41421-184">JavaScript 代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。</span><span class="sxs-lookup"><span data-stu-id="41421-184">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="41421-185">例如，下面的代码定义了一个 .NET 扩展方法，该方法的作用是将鼠标单击事件发送到某个元素：</span><span class="sxs-lookup"><span data-stu-id="41421-185">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="41421-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="41421-186">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="41421-187">在 C# 代码中使用 [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) 来聚焦元素，该元素内置于 Blazor 框架，并与元素引用结合使用。</span><span class="sxs-lookup"><span data-stu-id="41421-187">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="41421-188">若要调用不返回值的 JavaScript 函数，请使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="41421-188">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="41421-189">下面的代码使用捕获的 <xref:Microsoft.AspNetCore.Components.ElementReference> 调用上面的 JavaScript 函数，触发客户端 `Click` 事件：</span><span class="sxs-lookup"><span data-stu-id="41421-189">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="41421-190">若要使用扩展方法，请创建接收 <xref:Microsoft.JSInterop.IJSRuntime> 实例的静态扩展方法：</span><span class="sxs-lookup"><span data-stu-id="41421-190">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="41421-191">`clickElement` 方法在对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="41421-191">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="41421-192">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `TriggerClickEvent` 方法：</span><span class="sxs-lookup"><span data-stu-id="41421-192">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="41421-193">仅在呈现组件后填充 `exampleButton` 变量。</span><span class="sxs-lookup"><span data-stu-id="41421-193">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="41421-194">如果将未填充的 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="41421-194">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="41421-195">若要在组件完成呈现后操作元素引用，请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 组件生命周期方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="41421-195">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="41421-196">使用泛型类型并返回值时，请使用 <xref:System.Threading.Tasks.ValueTask%601>：</span><span class="sxs-lookup"><span data-stu-id="41421-196">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="41421-197">`GenericMethod` 在具有类型的对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="41421-197">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="41421-198">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="41421-198">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="41421-199">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="41421-199">Reference elements across components</span></span>

<span data-ttu-id="41421-200">不能在组件之间传递 <xref:Microsoft.AspNetCore.Components.ElementReference>，因为：</span><span class="sxs-lookup"><span data-stu-id="41421-200">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="41421-201">仅在呈现组件之后（即在执行组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 方法期间或之后），才能保证实例存在。</span><span class="sxs-lookup"><span data-stu-id="41421-201">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="41421-202"><xref:Microsoft.AspNetCore.Components.ElementReference> 是 [`struct`](/csharp/language-reference/builtin-types/struct)，不能作为[组件参数](xref:blazor/components/index#component-parameters)传递。</span><span class="sxs-lookup"><span data-stu-id="41421-202">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="41421-203">若要使父组件可以向其他组件提供元素引用，父组件可以：</span><span class="sxs-lookup"><span data-stu-id="41421-203">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="41421-204">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="41421-204">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="41421-205">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="41421-205">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="41421-206">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="41421-206">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="41421-207">下面的 Blazor WebAssembly 示例阐释了这种方法。</span><span class="sxs-lookup"><span data-stu-id="41421-207">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="41421-208">在 `wwwroot/index.html` 的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="41421-208">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="41421-209">在 `wwwroot/index.html` 的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="41421-209">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="41421-210">`Pages/Index.razor`（父组件）：</span><span class="sxs-lookup"><span data-stu-id="41421-210">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="41421-211">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="41421-211">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="41421-212">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="41421-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="41421-213">`Shared/SurveyPrompt.razor`（子组件）：</span><span class="sxs-lookup"><span data-stu-id="41421-213">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="41421-214">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="41421-214">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="41421-215">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="41421-215">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="41421-216">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="41421-216">Harden JS interop calls</span></span>

<span data-ttu-id="41421-217">JS 互操作可能会由于网络错误而失败，因此应视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="41421-217">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="41421-218">默认情况下，Blazor Server 应用会在一分钟后，使服务器上的 JS 互操作调用超时。</span><span class="sxs-lookup"><span data-stu-id="41421-218">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="41421-219">如果应用可以容忍更激进的超时，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="41421-219">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="41421-220">在 `Startup.ConfigureServices` 中全局指定超时：</span><span class="sxs-lookup"><span data-stu-id="41421-220">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="41421-221">对于组件代码中的每个调用，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="41421-221">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="41421-222">有关资源耗尽的详细信息，请参阅 <xref:blazor/security/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="41421-222">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="41421-223">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="41421-223">Avoid circular object references</span></span>

<span data-ttu-id="41421-224">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="41421-224">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="41421-225">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="41421-225">.NET method calls.</span></span>
* <span data-ttu-id="41421-226">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="41421-226">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="41421-227">有关详细信息，请参阅[循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)。</span><span class="sxs-lookup"><span data-stu-id="41421-227">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="41421-228">Blazor JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="41421-228">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="41421-229">Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="41421-229">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="41421-230">JavaScript 隔离具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="41421-230">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="41421-231">导入的 JavaScript 不再污染全局命名空间。</span><span class="sxs-lookup"><span data-stu-id="41421-231">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="41421-232">库和组件的使用者不需要导入相关的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="41421-232">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="41421-233">例如，以下 JavaScript 模块导出用于显示浏览器提示的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="41421-233">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="41421-234">将前面的 JavaScript 模块作为静态 Web 资产 (`wwwroot/exampleJsInterop.js`) 添加到 .NET 库，然后通过调用 <xref:Microsoft.JSInterop.IJSRuntime> 服务上的 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 将该模块导入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="41421-234">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="41421-235">以下示例将服务作为 `js`（未显示）注入：</span><span class="sxs-lookup"><span data-stu-id="41421-235">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="41421-236">上例中的 `import` 标识符是专门用于导入 JavaScript 模块的特殊标识符。</span><span class="sxs-lookup"><span data-stu-id="41421-236">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="41421-237">使用模块的稳定静态 Web 资产路径 `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 指定模块。</span><span class="sxs-lookup"><span data-stu-id="41421-237">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="41421-238">若要创建 JavaScript 文件的正确静态资产路径，需要当前目录 (`./`) 的路径段。</span><span class="sxs-lookup"><span data-stu-id="41421-238">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="41421-239">动态导入模块需要网络请求，因此只能通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 来异步实现。</span><span class="sxs-lookup"><span data-stu-id="41421-239">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="41421-240">占位符 `{LIBRARY NAME}` 是库的名称。</span><span class="sxs-lookup"><span data-stu-id="41421-240">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="41421-241">占位符 `{PATH UNDER WWWROOT}` 是 `wwwroot` 下脚本的路径。</span><span class="sxs-lookup"><span data-stu-id="41421-241">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="41421-242"><xref:Microsoft.JSInterop.IJSRuntime> 将模块作为 `IJSObjectReference` 导入，它表示对 .NET 代码中 JavaScript 对象的引用。</span><span class="sxs-lookup"><span data-stu-id="41421-242"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="41421-243">使用 `IJSObjectReference` 调用从模块导出的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="41421-243">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="41421-244">`IJSInProcessObjectReference` 表示对某个 JavaScript 对象的引用，该对象的函数可以同步进行调用。</span><span class="sxs-lookup"><span data-stu-id="41421-244">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="41421-245">使用呈现 UI 的 JavaScript 库（DOM 元素）</span><span class="sxs-lookup"><span data-stu-id="41421-245">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="41421-246">有时你可能需要使用在浏览器 DOM 内生成可见用户界面元素的 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="41421-246">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="41421-247">乍一想，这似乎很难，因为 Blazor 的 diffing 系统依赖于对 DOM 元素树的控制，并且如果某个外部代码使 DOM 树发生变化并为了应用 diff 而使其机制失效，就会产生错误。</span><span class="sxs-lookup"><span data-stu-id="41421-247">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="41421-248">这并不是一个特定于 Blazor 的限制。</span><span class="sxs-lookup"><span data-stu-id="41421-248">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="41421-249">任何基于 diff 的 UI 框架都会面临同样的问题。</span><span class="sxs-lookup"><span data-stu-id="41421-249">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="41421-250">幸运的是，将外部生成的 UI 可靠地嵌入到 Blazor 组件 UI 非常简单。</span><span class="sxs-lookup"><span data-stu-id="41421-250">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="41421-251">推荐的方法是让组件的代码（`.razor` 文件）生成一个空元素。</span><span class="sxs-lookup"><span data-stu-id="41421-251">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="41421-252">就 Blazor 的 diffing 系统而言，该元素始终为空，这样呈现器就不会递归到元素中，而是保留元素内容不变。</span><span class="sxs-lookup"><span data-stu-id="41421-252">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="41421-253">这样就可以安全地用外部托管的任意内容来填充元素。</span><span class="sxs-lookup"><span data-stu-id="41421-253">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="41421-254">以下示例演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="41421-254">The following example demonstrates the concept.</span></span> <span data-ttu-id="41421-255">在 `if` 语句中，当 `firstRender` 为 `true` 时，使用 `myElement` 来执行某些操作。</span><span class="sxs-lookup"><span data-stu-id="41421-255">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="41421-256">例如，调用某个外部 JavaScript 库来填充它。</span><span class="sxs-lookup"><span data-stu-id="41421-256">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="41421-257">Blazor 保留元素内容不变，直到此组件本身被删除。</span><span class="sxs-lookup"><span data-stu-id="41421-257">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="41421-258">删除组件时，会同时删除组件的整个 DOM 子树。</span><span class="sxs-lookup"><span data-stu-id="41421-258">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="41421-259">为了更详细地说明，请思考以下组件，该组件使用[开源 Mapbox API](https://www.mapbox.com/) 呈现交互式地图：</span><span class="sxs-lookup"><span data-stu-id="41421-259">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="41421-260">相应的 JavaScript 模块（应置于 `wwwroot/mapComponent.js` 处）如下所示：</span><span class="sxs-lookup"><span data-stu-id="41421-260">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="41421-261">在前面的示例中，将字符串 `{ACCESS TOKEN}` 替换为可从 https://account.mapbox.com 中获取的有效访问令牌。</span><span class="sxs-lookup"><span data-stu-id="41421-261">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="41421-262">为生成正确的样式，将以下样式表标记添加到主机 HTML 页面中（`index.html` 或 `_Host.cshtml`）：</span><span class="sxs-lookup"><span data-stu-id="41421-262">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="41421-263">前面的示例会生成一个交互式地图 UI，其中用户：</span><span class="sxs-lookup"><span data-stu-id="41421-263">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="41421-264">拖动鼠标可以滚动或缩放。</span><span class="sxs-lookup"><span data-stu-id="41421-264">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="41421-265">单击相关按钮可跳转到预定义的位置。</span><span class="sxs-lookup"><span data-stu-id="41421-265">Click buttons to jump to predefined locations.</span></span>

![Mapbox 的日本东京街道地图，其中设有可用于选择英国布里斯托尔和日本东京的按钮](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="41421-267">要了解的要点如下：</span><span class="sxs-lookup"><span data-stu-id="41421-267">The key points to understand are:</span></span>

 * <span data-ttu-id="41421-268">就 Blazor 而言，具有 `@ref="mapElement"` 的 `<div>` 保留为空。</span><span class="sxs-lookup"><span data-stu-id="41421-268">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="41421-269">这样随着时间推移，`mapbox-gl.js` 填充和修改其内容是安全的。</span><span class="sxs-lookup"><span data-stu-id="41421-269">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="41421-270">可以将此方法与呈现 UI 的任何 JavaScript 库配合使用。</span><span class="sxs-lookup"><span data-stu-id="41421-270">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="41421-271">甚至可以在 Blazor 组件中嵌入第三方 JavaScript SPA 框架中的组件，只要它们不会尝试访问和改变页面的其他部分。</span><span class="sxs-lookup"><span data-stu-id="41421-271">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="41421-272">对于外部 JavaScript 代码，修改 Blazor 不视为空元素的元素是 *不* 安全的。</span><span class="sxs-lookup"><span data-stu-id="41421-272">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="41421-273">使用此方法时，请记得有关 Blazor 保留或销毁 DOM 元素的方式的规则。</span><span class="sxs-lookup"><span data-stu-id="41421-273">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="41421-274">在前面的示例中，组件之所以能够安全处理按钮单击事件并更新现有的地图实例，是因为默认在可能的情况下保留 DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="41421-274">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="41421-275">如果之前要从 `@foreach` 循环内呈现地图元素的列表，则需要使用 `@key` 来确保保留组件实例。</span><span class="sxs-lookup"><span data-stu-id="41421-275">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="41421-276">否则，列表数据的更改可能导致组件实例以不合适的方式保留以前实例的状态。</span><span class="sxs-lookup"><span data-stu-id="41421-276">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="41421-277">有关详细信息，请参阅[使用 @key 保留元素和组件](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)。</span><span class="sxs-lookup"><span data-stu-id="41421-277">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="41421-278">此外，上面的示例演示了如何在 ES6 模块中封装 JavaScript 逻辑和依赖关系，并使用 `import` 标识符动态加载该模块。</span><span class="sxs-lookup"><span data-stu-id="41421-278">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="41421-279">有关详细信息，请参阅 [JavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="41421-279">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="41421-280">对 JS 互操作调用的大小限制</span><span class="sxs-lookup"><span data-stu-id="41421-280">Size limits on JS interop calls</span></span>

<span data-ttu-id="41421-281">在 Blazor WebAssembly 中，框架对 JS 互操作输入和输出的大小不施加限制。</span><span class="sxs-lookup"><span data-stu-id="41421-281">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="41421-282">在 Blazor Server 中，JS 互操作调用的大小受中心方法允许的最大传入 SignalR 消息大小限制，该限制由 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>（默认值：32 KB）实施。</span><span class="sxs-lookup"><span data-stu-id="41421-282">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="41421-283">JS 到 .NET 的 SignalR 消息大于 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 时会引发错误。</span><span class="sxs-lookup"><span data-stu-id="41421-283">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="41421-284">框架对从中心到客户端的 SignalR 消息大小不施加限制。</span><span class="sxs-lookup"><span data-stu-id="41421-284">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="41421-285">有关详细信息，请参阅 <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>。</span><span class="sxs-lookup"><span data-stu-id="41421-285">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="41421-286">JS 模块</span><span class="sxs-lookup"><span data-stu-id="41421-286">JS modules</span></span>

<span data-ttu-id="41421-287">对于 JS 隔离，JS 互操作适用于浏览器针对 [EcmaScript 模块 (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)) 的默认支持。</span><span class="sxs-lookup"><span data-stu-id="41421-287">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="41421-288">未封装的 JS 互操作</span><span class="sxs-lookup"><span data-stu-id="41421-288">Unmarshalled JS interop</span></span>

<span data-ttu-id="41421-289">当针对 JS 互操作序列化 .NET 对象并且满足以下任一条件时，Blazor WebAssembly 组件的性能可能会较差：</span><span class="sxs-lookup"><span data-stu-id="41421-289">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="41421-290">大量 .NET 对象迅速地进行序列化。</span><span class="sxs-lookup"><span data-stu-id="41421-290">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="41421-291">示例：通过移动输入设备（如旋转鼠标滚轮）来进行 JS 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="41421-291">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="41421-292">对于 JS 互操作，必须序列化大型 .NET 对象或多个 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="41421-292">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="41421-293">示例：JS 互操作调用需要序列化数十个文件。</span><span class="sxs-lookup"><span data-stu-id="41421-293">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="41421-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> 表示对某个 JavaScript 对象的引用，该对象的函数无需 .NET 数据序列化开销即可调用。</span><span class="sxs-lookup"><span data-stu-id="41421-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="41421-295">在以下示例中：</span><span class="sxs-lookup"><span data-stu-id="41421-295">In the following example:</span></span>

* <span data-ttu-id="41421-296">包含字符串和整数的 [struct](/dotnet/csharp/language-reference/builtin-types/struct) 会以非序列化方式传递给 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="41421-296">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="41421-297">JavaScript 函数处理数据，并将布尔或字符串返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="41421-297">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="41421-298">JavaScript 字符串无法直接转换为 .NET `string` 对象。</span><span class="sxs-lookup"><span data-stu-id="41421-298">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="41421-299">`unmarshalledFunctionReturnString` 函数调用 `BINDING.js_string_to_mono_string` 来管理 Javascript 字符串的转换。</span><span class="sxs-lookup"><span data-stu-id="41421-299">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="41421-300">以下示例不是此方案的典型用例，因为传递给 JavaScript 的 [struct](/dotnet/csharp/language-reference/builtin-types/struct) 不会导致组件性能变差。</span><span class="sxs-lookup"><span data-stu-id="41421-300">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="41421-301">该示例仅使用一个小型对象来演示传递未序列化 .NET 数据的概念。</span><span class="sxs-lookup"><span data-stu-id="41421-301">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="41421-302">`wwwroot/index.html` 中 `<script>` 块的内容或 `wwwroot/index.html` 引用的外部 Javascript 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="41421-302">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="41421-303">`js_string_to_mono_string` 函数的名称、行为和存在可能会在 .NET 的将来版本中更改。</span><span class="sxs-lookup"><span data-stu-id="41421-303">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="41421-304">例如：</span><span class="sxs-lookup"><span data-stu-id="41421-304">For example:</span></span>
>
> * <span data-ttu-id="41421-305">函数可能已重命名。</span><span class="sxs-lookup"><span data-stu-id="41421-305">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="41421-306">函数本身可能会被删除，以便能够通过框架自动转换字符串。</span><span class="sxs-lookup"><span data-stu-id="41421-306">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="41421-307">`Pages/UnmarshalledJSInterop.razor`（URL：`/unmarshalled-js-interop`）：</span><span class="sxs-lookup"><span data-stu-id="41421-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="41421-308">如果 `IJSUnmarshalledObjectReference` 实例未使用 C# 代码释放，则可以使用 JavaScript 释放。</span><span class="sxs-lookup"><span data-stu-id="41421-308">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="41421-309">以下 `dispose` 函数在从 JavaScript 调用时释放对象引用：</span><span class="sxs-lookup"><span data-stu-id="41421-309">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="41421-310">可以使用 `js_typed_array_to_array` 将数组类型从 JavaScript 对象转换为 .NET 对象，但 JavaScript 数组必须为类型化数组。</span><span class="sxs-lookup"><span data-stu-id="41421-310">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="41421-311">可以使用 C# 代码将 JavaScript 中的数组作为 .NET 对象数组 (`object[]`) 进行读取。</span><span class="sxs-lookup"><span data-stu-id="41421-311">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="41421-312">可以转换其他数据类型（如字符串数组），但需要创建一个新的 Mono 数组对象 (`mono_obj_array_new`) 并设置其值 (`mono_obj_array_set`)。</span><span class="sxs-lookup"><span data-stu-id="41421-312">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="41421-313">Blazor 框架提供的 JavaScript 函数（如 `js_typed_array_to_array`、`mono_obj_array_new` 和 `mono_obj_array_set`）可能会在 .NET 的未来版本中进行名称更改、行为更改或删除。</span><span class="sxs-lookup"><span data-stu-id="41421-313">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41421-314">其他资源</span><span class="sxs-lookup"><span data-stu-id="41421-314">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="41421-315">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="41421-315">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
