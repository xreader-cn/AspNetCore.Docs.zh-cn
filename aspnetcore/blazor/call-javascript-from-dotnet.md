---
title: 在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数
author: guardrex
description: 了解如何在 Blazor 应用中从 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 26202c45e49e64117d35fe6f1e9a65c4acc170fb
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105085"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="0d021-103">在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="0d021-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="0d021-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0d021-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0d021-105">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="0d021-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="0d021-106">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="0d021-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="0d021-107">本文介绍如何从 .NET 调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="0d021-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="0d021-108">有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。</span><span class="sxs-lookup"><span data-stu-id="0d021-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="0d021-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d021-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0d021-110">若要从 .NET 调入 JavaScript，请使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="0d021-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="0d021-111">若要发出 JS 互操作调用，请在组件中注入 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。</span><span class="sxs-lookup"><span data-stu-id="0d021-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="0d021-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 需要使用你要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="0d021-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="0d021-113">函数标识符相对于全局范围 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="0d021-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="0d021-114">如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="0d021-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="0d021-115">无需在调用函数之前进行注册。</span><span class="sxs-lookup"><span data-stu-id="0d021-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="0d021-116">返回类型 `T` 也必须可进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="0d021-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="0d021-117">`T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="0d021-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="0d021-118">对于启用了预呈现的 Blazor 服务器应用，初始预呈现期间无法调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="0d021-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="0d021-119">在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="0d021-120">有关详细信息，请参阅[检测 Blazor 服务器应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="0d021-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="0d021-121">下面的示例基于 [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="0d021-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="0d021-122">此示例展示了如何通过 C# 方法调用 JavaScript 函数，以将要求从开发人员代码卸载到现有 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="0d021-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="0d021-123">JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="0d021-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="0d021-124">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="0d021-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="0d021-125">JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (.js) 加载：</span><span class="sxs-lookup"><span data-stu-id="0d021-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="0d021-126">以下组件：</span><span class="sxs-lookup"><span data-stu-id="0d021-126">The following component:</span></span>

* <span data-ttu-id="0d021-127">在选择了组件按钮（“转换数组”）时使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="0d021-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="0d021-128">调用 JavaScript 函数之后，传递的数组会转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="0d021-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="0d021-129">该字符串会返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="0d021-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="0d021-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="0d021-130">IJSRuntime</span></span>

<span data-ttu-id="0d021-131">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象，请采用以下任何方法：</span><span class="sxs-lookup"><span data-stu-id="0d021-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="0d021-132">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入 Razor 组件 (.razor) 中：</span><span class="sxs-lookup"><span data-stu-id="0d021-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="0d021-133">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="0d021-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0d021-134">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="0d021-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="0d021-135">将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入一个类 (.cs)：</span><span class="sxs-lookup"><span data-stu-id="0d021-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="0d021-136">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="0d021-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0d021-137">该函数通过 `JSRuntime.InvokeAsync` 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="0d021-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="0d021-138">对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：</span><span class="sxs-lookup"><span data-stu-id="0d021-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="0d021-139">在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="0d021-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="0d021-140">`showPrompt`：生成一个提示，用于接受用户输入（用户名），并将用户名返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="0d021-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="0d021-141">`displayWelcome`：将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="0d021-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="0d021-142">wwwroot/exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="0d021-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="0d021-143">将引用 JavaScript 文件的 `<script>` 标记置于 wwwroot/index.html 文件 (Blazor WebAssembly) 或 Pages/_Host.cshtml 文件（Blazor 服务器）中。</span><span class="sxs-lookup"><span data-stu-id="0d021-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="0d021-144">wwwroot/index.html (Blazor WebAssembly)：</span><span class="sxs-lookup"><span data-stu-id="0d021-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="0d021-145">Pages/_Host.cshtml（Blazor 服务器）：</span><span class="sxs-lookup"><span data-stu-id="0d021-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="0d021-146">请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="0d021-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="0d021-147">.NET 方法通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 与 exampleJsInterop.js 文件中的 JavaScript 函数进行互操作。</span><span class="sxs-lookup"><span data-stu-id="0d021-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0d021-148"><xref:Microsoft.JSInterop.IJSRuntime> 抽象是异步的，以便可以实现 Blazor 服务器方案。</span><span class="sxs-lookup"><span data-stu-id="0d021-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="0d021-149">如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 并改为调用 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>。</span><span class="sxs-lookup"><span data-stu-id="0d021-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="0d021-150">建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="0d021-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="0d021-151">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="0d021-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="0d021-152">该组件：</span><span class="sxs-lookup"><span data-stu-id="0d021-152">The component:</span></span>

* <span data-ttu-id="0d021-153">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="0d021-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="0d021-154">将文本返回给组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="0d021-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="0d021-155">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="0d021-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="0d021-156">Pages/JSInterop.razor：</span><span class="sxs-lookup"><span data-stu-id="0d021-156">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
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

1. <span data-ttu-id="0d021-157">通过选择组件的“触发 JavaScript 提示符”按钮来执行 `TriggerJsPrompt` 时，则会调用在 wwwroot/exampleJsInterop.js 文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="0d021-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="0d021-158">`showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="0d021-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="0d021-159">组件将用户的名称存储在本地变量 `name` 中。</span><span class="sxs-lookup"><span data-stu-id="0d021-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="0d021-160">存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。</span><span class="sxs-lookup"><span data-stu-id="0d021-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="0d021-161">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="0d021-161">Call a void JavaScript function</span></span>

<span data-ttu-id="0d021-162">返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="0d021-163">检测 Blazor 服务器应用进行预呈现的时间</span><span class="sxs-lookup"><span data-stu-id="0d021-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="0d021-164">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="0d021-164">Capture references to elements</span></span>

<span data-ttu-id="0d021-165">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="0d021-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="0d021-166">例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。</span><span class="sxs-lookup"><span data-stu-id="0d021-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="0d021-167">使用以下方法在组件中捕获对 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="0d021-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="0d021-168">向 HTML 元素添加 `@ref` 属性。</span><span class="sxs-lookup"><span data-stu-id="0d021-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="0d021-169">定义一个类型为 <xref:Microsoft.AspNetCore.Components.ElementReference> 字段，其名称与 `@ref` 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="0d021-169">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="0d021-170">以下示例演示如何捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="0d021-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="0d021-171">只使用元素引用改变不与 Blazor 交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="0d021-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="0d021-172">当第三方 API 向元素提供内容时，此方案十分有用。</span><span class="sxs-lookup"><span data-stu-id="0d021-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="0d021-173">由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。</span><span class="sxs-lookup"><span data-stu-id="0d021-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="0d021-174">在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：</span><span class="sxs-lookup"><span data-stu-id="0d021-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="0d021-175">如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="0d021-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="0d021-176">就 .NET 代码而言，<xref:Microsoft.AspNetCore.Components.ElementReference> 是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="0d021-176">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="0d021-177">可以对 <xref:Microsoft.AspNetCore.Components.ElementReference> 执行的唯一操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="0d021-177">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="0d021-178">执行此操作时，JavaScript 端代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。</span><span class="sxs-lookup"><span data-stu-id="0d021-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="0d021-179">例如，以下代码定义一个 .NET 扩展方法，通过该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="0d021-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="0d021-180">exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="0d021-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="0d021-181">若要调用不返回值的 JavaScript 函数，请使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="0d021-181">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0d021-182">下面的代码通过使用捕获的 <xref:Microsoft.AspNetCore.Components.ElementReference> 调用前面的 JavaScript 函数，在用户名输入上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="0d021-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="0d021-183">若要使用扩展方法，请创建接收 <xref:Microsoft.JSInterop.IJSRuntime> 实例的静态扩展方法：</span><span class="sxs-lookup"><span data-stu-id="0d021-183">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="0d021-184">`Focus` 方法在对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="0d021-185">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `Focus` 方法：</span><span class="sxs-lookup"><span data-stu-id="0d021-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="0d021-186">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="0d021-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="0d021-187">如果将未填充的 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="0d021-187">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="0d021-188">若要在组件完成呈现之后操作元素引用（用于对元素设置初始焦点），请使用 [OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="0d021-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="0d021-189">使用泛型类型并返回值时，请使用 <xref:System.Threading.Tasks.ValueTask%601>：</span><span class="sxs-lookup"><span data-stu-id="0d021-189">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="0d021-190">`GenericMethod` 在具有类型的对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="0d021-191">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="0d021-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="0d021-192">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="0d021-192">Reference elements across components</span></span>

<span data-ttu-id="0d021-193"><xref:Microsoft.AspNetCore.Components.ElementReference> 仅保证在组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。</span><span class="sxs-lookup"><span data-stu-id="0d021-193">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="0d021-194">若要使父组件可以向其他组件提供元素引用，父组件可以：</span><span class="sxs-lookup"><span data-stu-id="0d021-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="0d021-195">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="0d021-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="0d021-196">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="0d021-196">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="0d021-197">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="0d021-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="0d021-198">以下 Blazor WebAssembly 示例演示了该方法。</span><span class="sxs-lookup"><span data-stu-id="0d021-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="0d021-199">在 wwwroot/index.html 的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="0d021-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="0d021-200">在 wwwroot/index.html 的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="0d021-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="0d021-201">Pages/Index.razor（父组件）：</span><span class="sxs-lookup"><span data-stu-id="0d021-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="0d021-202">Pages/Index.razor.cs：</span><span class="sxs-lookup"><span data-stu-id="0d021-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
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

<span data-ttu-id="0d021-203">Shared/SurveyPrompt.razor（子组件）：</span><span class="sxs-lookup"><span data-stu-id="0d021-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="0d021-204">Shared/SurveyPrompt.razor.cs：</span><span class="sxs-lookup"><span data-stu-id="0d021-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="0d021-205">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="0d021-205">Harden JS interop calls</span></span>

<span data-ttu-id="0d021-206">JS 互操作可能会由于网络错误而失败，因此应视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="0d021-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="0d021-207">默认情况下，Blazor 服务器应用会在一分钟后，使服务器上的 JS 互操作调用超时。</span><span class="sxs-lookup"><span data-stu-id="0d021-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="0d021-208">如果应用可以容忍更激进的超时，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="0d021-208">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="0d021-209">在 `Startup.ConfigureServices` 中全局指定超时：</span><span class="sxs-lookup"><span data-stu-id="0d021-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="0d021-210">对于组件代码中的每个调用，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="0d021-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="0d021-211">有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="0d021-211">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="0d021-212">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="0d021-212">Avoid circular object references</span></span>

<span data-ttu-id="0d021-213">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="0d021-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="0d021-214">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-214">.NET method calls.</span></span>
* <span data-ttu-id="0d021-215">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="0d021-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="0d021-216">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="0d021-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="0d021-217">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="0d021-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="0d021-218">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="0d021-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="0d021-219">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d021-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="0d021-220">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="0d021-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="0d021-221">[在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="0d021-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
