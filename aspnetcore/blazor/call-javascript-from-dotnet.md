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
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722470"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="ac8c9-103">在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="ac8c9-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="ac8c9-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ac8c9-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ac8c9-105">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="ac8c9-106">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="ac8c9-107">本文介绍如何从 .NET 调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="ac8c9-108">有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="ac8c9-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ac8c9-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ac8c9-110">若要从 .NET 调入 JavaScript，请使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="ac8c9-111">若要发出 JS 互操作调用，请在组件中注入 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="ac8c9-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 需要使用你要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="ac8c9-113">函数标识符相对于全局范围 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="ac8c9-114">如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="ac8c9-115">无需在调用函数之前进行注册。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="ac8c9-116">返回类型 `T` 也必须可进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="ac8c9-117">`T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="ac8c9-118">对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="ac8c9-119">在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="ac8c9-120">有关详细信息，请参阅[检测 Blazor Server 应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="ac8c9-121">下面的示例基于 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="ac8c9-122">此示例展示了如何通过 C# 方法调用 JavaScript 函数，以将要求从开发人员代码卸载到现有 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="ac8c9-123">JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="ac8c9-124">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="ac8c9-125">JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (`.js`) 加载：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="ac8c9-126">以下组件：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-126">The following component:</span></span>

* <span data-ttu-id="ac8c9-127">在选择了组件按钮（“`Convert Array`”）后，使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="ac8c9-128">调用 JavaScript 函数之后，传递的数组会转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="ac8c9-129">该字符串会返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="ac8c9-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="ac8c9-130">IJSRuntime</span></span>

<span data-ttu-id="ac8c9-131">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象，请采用以下任何方法：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="ac8c9-132">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入 Razor 组件 (`.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="ac8c9-133">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="ac8c9-134">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="ac8c9-135">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入类 (`.cs`)：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="ac8c9-136">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="ac8c9-137">该函数通过 `JSRuntime.InvokeAsync` 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="ac8c9-138">对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="ac8c9-139">在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="ac8c9-140">`showPrompt`：生成一个提示，用于接受用户输入（用户名），并将用户名返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="ac8c9-141">`displayWelcome`：将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="ac8c9-142">`wwwroot/exampleJsInterop.js`：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="ac8c9-143">将引用 JavaScript 文件的 `<script>` 标记置于 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="ac8c9-144">`wwwroot/index.html` (Blazor WebAssembly)：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="ac8c9-145">`Pages/_Host.cshtml` (Blazor Server)：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="ac8c9-146">请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="ac8c9-147">.NET 方法通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 与 `exampleJsInterop.js` 文件中的 JavaScript 函数进行互操作。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="ac8c9-148"><xref:Microsoft.JSInterop.IJSRuntime> 抽象是异步的，以便可以实现 Blazor Server 方案。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="ac8c9-149">如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 并改为调用 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="ac8c9-150">建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ac8c9-151">若要在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离，请参阅 [BlazorJavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)部分。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="ac8c9-152">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="ac8c9-153">该组件：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-153">The component:</span></span>

* <span data-ttu-id="ac8c9-154">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="ac8c9-155">将文本返回给组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="ac8c9-156">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="ac8c9-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="ac8c9-157">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="ac8c9-158">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="ac8c9-159">通过选择组件的“`Trigger JavaScript Prompt`”按钮来执行 `TriggerJsPrompt` 时，则会调用在 `wwwroot/exampleJsInterop.js` 文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="ac8c9-160">`showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="ac8c9-161">组件将用户的名称存储在本地变量 `name` 中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="ac8c9-162">存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="ac8c9-163">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="ac8c9-163">Call a void JavaScript function</span></span>

<span data-ttu-id="ac8c9-164">返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="ac8c9-165">检测 Blazor Server 应用进行预呈现的时间</span><span class="sxs-lookup"><span data-stu-id="ac8c9-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="ac8c9-166">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="ac8c9-166">Capture references to elements</span></span>

<span data-ttu-id="ac8c9-167">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="ac8c9-168">例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="ac8c9-169">使用以下方法在组件中捕获对 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="ac8c9-170">向 HTML 元素添加 `@ref` 属性。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="ac8c9-171">定义一个类型为 <xref:Microsoft.AspNetCore.Components.ElementReference> 字段，其名称与 `@ref` 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="ac8c9-172">以下示例演示如何捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="ac8c9-173">只使用元素引用改变不与 Blazor 交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="ac8c9-174">当第三方 API 向元素提供内容时，此方案十分有用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="ac8c9-175">由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="ac8c9-176">在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="ac8c9-177">如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="ac8c9-178">就 .NET 代码而言，<xref:Microsoft.AspNetCore.Components.ElementReference> 是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="ac8c9-179">可以对 <xref:Microsoft.AspNetCore.Components.ElementReference> 执行的唯一操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="ac8c9-180">执行此操作时，JavaScript 端代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="ac8c9-181">例如，以下代码定义一个 .NET 扩展方法，通过该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="ac8c9-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="ac8c9-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="ac8c9-183">若要调用不返回值的 JavaScript 函数，请使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ac8c9-184">下面的代码通过使用捕获的 <xref:Microsoft.AspNetCore.Components.ElementReference> 调用前面的 JavaScript 函数，在用户名输入上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="ac8c9-185">若要使用扩展方法，请创建接收 <xref:Microsoft.JSInterop.IJSRuntime> 实例的静态扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="ac8c9-186">`Focus` 方法在对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="ac8c9-187">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `Focus` 方法：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="ac8c9-188">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="ac8c9-189">如果将未填充的 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="ac8c9-190">若要在组件完成呈现之后操作元素引用（用于对元素设置初始焦点），请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 组件生命周期方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="ac8c9-191">使用泛型类型并返回值时，请使用 <xref:System.Threading.Tasks.ValueTask%601>：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="ac8c9-192">`GenericMethod` 在具有类型的对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="ac8c9-193">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="ac8c9-194">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="ac8c9-194">Reference elements across components</span></span>

<span data-ttu-id="ac8c9-195"><xref:Microsoft.AspNetCore.Components.ElementReference> 仅保证在组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="ac8c9-196">若要使父组件可以向其他组件提供元素引用，父组件可以：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="ac8c9-197">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="ac8c9-198">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="ac8c9-199">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="ac8c9-200">下面的 Blazor WebAssembly 示例阐释了这种方法。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="ac8c9-201">在 `wwwroot/index.html` 的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="ac8c9-202">在 `wwwroot/index.html` 的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="ac8c9-203">`Pages/Index.razor`（父组件）：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="ac8c9-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac8c9-204">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="ac8c9-205">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="ac8c9-206">`Shared/SurveyPrompt.razor`（子组件）：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="ac8c9-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac8c9-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="ac8c9-208">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="ac8c9-209">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="ac8c9-209">Harden JS interop calls</span></span>

<span data-ttu-id="ac8c9-210">JS 互操作可能会由于网络错误而失败，因此应视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="ac8c9-211">默认情况下，Blazor Server 应用会在一分钟后，使服务器上的 JS 互操作调用超时。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="ac8c9-212">如果应用可以容忍更激进的超时，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="ac8c9-213">在 `Startup.ConfigureServices` 中全局指定超时：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="ac8c9-214">对于组件代码中的每个调用，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="ac8c9-215">有关资源耗尽的详细信息，请参阅 <xref:blazor/security/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="ac8c9-216">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="ac8c9-216">Avoid circular object references</span></span>

<span data-ttu-id="ac8c9-217">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="ac8c9-218">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-218">.NET method calls.</span></span>
* <span data-ttu-id="ac8c9-219">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="ac8c9-220">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="ac8c9-221">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="ac8c9-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="ac8c9-222">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="ac8c9-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="ac8c9-223">Blazor JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="ac8c9-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="ac8c9-224">Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="ac8c9-225">JavaScript 隔离具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="ac8c9-226">导入的 JavaScript 不再污染全局命名空间。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="ac8c9-227">库和组件的使用者不需要导入相关的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="ac8c9-228">例如，以下 JavaScript 模块导出用于显示浏览器提示的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="ac8c9-229">将前面的 JavaScript 模块作为静态 Web 资产 (`wwwroot/exampleJsInterop.js`) 添加到 .NET 库，然后使用 <xref:Microsoft.JSInterop.IJSRuntime> 服务将该模块导入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="ac8c9-230">以下示例将服务作为 `jsRuntime`（未显示）注入：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="ac8c9-231">上例中的 `import` 标识符是专门用于导入 JavaScript 模块的特殊标识符。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="ac8c9-232">使用模块的稳定静态 Web 资产路径 `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 指定模块。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="ac8c9-233">占位符 `{LIBRARY NAME}` 是库的名称。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="ac8c9-234">占位符 `{PATH UNDER WWWROOT}` 是 `wwwroot` 下脚本的路径。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="ac8c9-235"><xref:Microsoft.JSInterop.IJSRuntime> 将模块作为 `JSObjectReference` 导入，它表示对 .NET 代码中 JavaScript 对象的引用。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="ac8c9-236">使用 `JSObjectReference` 调用从模块导出的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ac8c9-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="ac8c9-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="ac8c9-238">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="ac8c9-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="ac8c9-239">在 Blazor Server 应用中执行大型数据传输</span><span class="sxs-lookup"><span data-stu-id="ac8c9-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
