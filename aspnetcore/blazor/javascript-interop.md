---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor 应用中的 JavaScript 的 .NET 和 .NET 方法中调用 JavaScript 函数。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: d681eea5a5e876912bd614fba8ea45a464844496
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447160"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="67639-103">ASP.NET Core Blazor JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="67639-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="67639-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="67639-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="67639-105">Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="67639-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="67639-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="67639-107">从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="67639-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="67639-108">有时需要 .NET 代码才能调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="67639-109">例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。</span><span class="sxs-lookup"><span data-stu-id="67639-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="67639-110">此方案称为*JavaScript 互操作性*（*JS 互操作*）。</span><span class="sxs-lookup"><span data-stu-id="67639-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="67639-111">若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="67639-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="67639-112">若要发出 JS 互操作调用，请在组件中注入 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="67639-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="67639-113">`InvokeAsync<T>` 方法采用 JavaScript 函数的标识符，该标识符将与任意数量的 JSON 可序列化参数一起调用。</span><span class="sxs-lookup"><span data-stu-id="67639-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="67639-114">函数标识符相对于全局范围（`window`）。</span><span class="sxs-lookup"><span data-stu-id="67639-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="67639-115">如果要调用 `window.someScope.someFunction`，则 `someScope.someFunction`标识符。</span><span class="sxs-lookup"><span data-stu-id="67639-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="67639-116">无需在调用函数之前注册它。</span><span class="sxs-lookup"><span data-stu-id="67639-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="67639-117">返回类型 `T` 也必须是 JSON 可序列化的。</span><span class="sxs-lookup"><span data-stu-id="67639-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="67639-118">`T` 应匹配最适合于返回的 JSON 类型的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="67639-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="67639-119">对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="67639-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="67639-120">在建立与浏览器的连接后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="67639-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="67639-121">有关详细信息，请参阅[检测何时预呈现 Blazor 应用](#detect-when-a-blazor-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="67639-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="67639-122">下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="67639-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="67639-123">该示例演示如何从C#方法调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="67639-124">JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。</span><span class="sxs-lookup"><span data-stu-id="67639-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="67639-125">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="67639-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="67639-126">JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：</span><span class="sxs-lookup"><span data-stu-id="67639-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="67639-127">以下组件：</span><span class="sxs-lookup"><span data-stu-id="67639-127">The following component:</span></span>

* <span data-ttu-id="67639-128">选择组件按钮（**转换数组**）时使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="67639-129">调用 JavaScript 函数后，传递的数组将转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="67639-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="67639-130">将字符串返回到要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="67639-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="67639-131">使用 IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="67639-131">Use of IJSRuntime</span></span>

<span data-ttu-id="67639-132">若要使用 `IJSRuntime` 抽象，请采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="67639-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="67639-133">将 `IJSRuntime` 抽象插入 Razor 组件（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="67639-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="67639-134">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="67639-135">使用 `IJSRuntime.InvokeVoidAsync` 调用函数，并且不返回值：</span><span class="sxs-lookup"><span data-stu-id="67639-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="67639-136">将 `IJSRuntime` 抽象插入类（ *.cs*）：</span><span class="sxs-lookup"><span data-stu-id="67639-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="67639-137">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="67639-138">使用 `JSRuntime.InvokeAsync` 调用函数并返回值：</span><span class="sxs-lookup"><span data-stu-id="67639-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="67639-139">对于使用[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)的动态内容生成，请使用 `[Inject]` 特性：</span><span class="sxs-lookup"><span data-stu-id="67639-139">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="67639-140">本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="67639-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="67639-141">`showPrompt` &ndash; 会生成一个提示，以接受用户输入（用户的名称），并将名称返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="67639-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="67639-142">`displayWelcome` &ndash; 将来自调用方的欢迎消息分配给具有 `welcome``id` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="67639-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="67639-143">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="67639-144">将引用 JavaScript 文件的 `<script>` 标记放在*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/Blazor _Host*</span><span class="sxs-lookup"><span data-stu-id="67639-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="67639-145">*wwwroot/index.html* （Blazor WebAssembly）：</span><span class="sxs-lookup"><span data-stu-id="67639-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="67639-146">*Pages/_Host cshtml* （Blazor Server）：</span><span class="sxs-lookup"><span data-stu-id="67639-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="67639-147">请勿在组件文件中放置 `<script>` 标记，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="67639-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="67639-148">通过调用 `IJSRuntime.InvokeAsync<T>`，.NET 方法与*exampleJsInterop*文件中的 JavaScript 函数互操作。</span><span class="sxs-lookup"><span data-stu-id="67639-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="67639-149">`IJSRuntime` 抽象是异步的，以允许 Blazor 服务器方案。</span><span class="sxs-lookup"><span data-stu-id="67639-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="67639-150">如果应用是 Blazor WebAssembly 应用，并且希望同步调用 JavaScript 函数，则向下转换到 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。</span><span class="sxs-lookup"><span data-stu-id="67639-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="67639-151">建议大多数 JS 互操作库使用异步 Api，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="67639-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="67639-152">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="67639-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="67639-153">组件：</span><span class="sxs-lookup"><span data-stu-id="67639-153">The component:</span></span>

* <span data-ttu-id="67639-154">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="67639-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="67639-155">将文本返回到组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="67639-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="67639-156">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="67639-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="67639-157">*Pages/JSInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-157">*Pages/JSInterop.razor*:</span></span>

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
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="67639-158">通过选择组件的 "**触发器 JavaScript" 提示**按钮执行 `TriggerJsPrompt` 时，将调用*wwwroot/exampleJsInterop*文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="67639-159">`showPrompt` 函数接受 HTML 编码并返回到组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="67639-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="67639-160">组件将用户的名称存储在本地变量 `name`中。</span><span class="sxs-lookup"><span data-stu-id="67639-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="67639-161">存储在 `name` 中的字符串合并为一个欢迎消息，该消息将被传递给 JavaScript 函数，`displayWelcome`，该消息会将欢迎消息呈现到标题标记中。</span><span class="sxs-lookup"><span data-stu-id="67639-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="67639-162">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="67639-162">Call a void JavaScript function</span></span>

<span data-ttu-id="67639-163">返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数通过 `IJSRuntime.InvokeVoidAsync`调用。</span><span class="sxs-lookup"><span data-stu-id="67639-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="67639-164">检测何时预呈现 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="67639-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="67639-165">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="67639-165">Capture references to elements</span></span>

<span data-ttu-id="67639-166">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="67639-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="67639-167">例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素上调用类似命令的 Api，如 `focus` 或 `play`。</span><span class="sxs-lookup"><span data-stu-id="67639-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="67639-168">使用以下方法捕获对组件中 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="67639-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="67639-169">将 `@ref` 特性添加到 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="67639-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="67639-170">定义一个类型 `ElementReference` 字段，其名称与 `@ref` 属性的值相匹配。</span><span class="sxs-lookup"><span data-stu-id="67639-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="67639-171">下面的示例演示捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="67639-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="67639-172">只使用元素引用来改变不与 Blazor进行交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="67639-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="67639-173">当第三方 API 向元素提供内容时，此方案很有用。</span><span class="sxs-lookup"><span data-stu-id="67639-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="67639-174">由于 Blazor 不与元素交互，因此在 Blazor的元素和 DOM 的表示形式之间不存在冲突。</span><span class="sxs-lookup"><span data-stu-id="67639-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="67639-175">在下面的示例中，改变无序列表（`ul`）的内容很*危险*，因为 Blazor 与 DOM 交互以填充此元素的列表项（`<li>`）：</span><span class="sxs-lookup"><span data-stu-id="67639-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="67639-176">如果 JS 互操作改变了元素 `MyList` 的内容，并且 Blazor 尝试将差异应用到元素，则差异将与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="67639-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="67639-177">就 .NET 代码而言，`ElementReference` 是一种不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="67639-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="67639-178">使用 `ElementReference` 可以执行的*唯一*操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="67639-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="67639-179">当你执行此操作时，JavaScript 端代码将收到一个 `HTMLElement` 实例，该实例可用于普通 DOM Api。</span><span class="sxs-lookup"><span data-stu-id="67639-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="67639-180">例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="67639-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="67639-181">*exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="67639-182">若要调用不返回值的 JavaScript 函数，请使用 `IJSRuntime.InvokeVoidAsync`。</span><span class="sxs-lookup"><span data-stu-id="67639-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="67639-183">下面的代码通过使用捕获的 `ElementReference`调用前面的 JavaScript 函数，对用户名输入设置焦点：</span><span class="sxs-lookup"><span data-stu-id="67639-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="67639-184">若要使用扩展方法，请创建一个静态扩展方法，用于接收 `IJSRuntime` 实例：</span><span class="sxs-lookup"><span data-stu-id="67639-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="67639-185">在对象上直接调用 `Focus` 方法。</span><span class="sxs-lookup"><span data-stu-id="67639-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="67639-186">下面的示例假定 `Focus` 方法可从 `JsInteropClasses` 命名空间中获得：</span><span class="sxs-lookup"><span data-stu-id="67639-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="67639-187">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="67639-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="67639-188">如果将未填充 `ElementReference` 传递给 JavaScript 代码，JavaScript 代码将接收值 `null`。</span><span class="sxs-lookup"><span data-stu-id="67639-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="67639-189">若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用[OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="67639-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="67639-190">使用泛型类型并返回值时，请使用[ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1)：</span><span class="sxs-lookup"><span data-stu-id="67639-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="67639-191">直接在具有类型的对象上调用 `GenericMethod`。</span><span class="sxs-lookup"><span data-stu-id="67639-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="67639-192">下面的示例假定 `JsInteropClasses` 命名空间中提供了 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="67639-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="67639-193">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="67639-193">Reference elements across components</span></span>

<span data-ttu-id="67639-194">`ElementReference` 仅保证在组件的 `OnAfterRender` 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。</span><span class="sxs-lookup"><span data-stu-id="67639-194">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="67639-195">对于父组件，若要使元素引用可用于其他组件，则父组件可以：</span><span class="sxs-lookup"><span data-stu-id="67639-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="67639-196">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="67639-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="67639-197">在 `OnAfterRender` 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="67639-197">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="67639-198">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="67639-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="67639-199">以下 Blazor WebAssembly 示例阐释了该方法。</span><span class="sxs-lookup"><span data-stu-id="67639-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="67639-200">在*wwwroot/index.html*的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="67639-200">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="67639-201">在*wwwroot/index.html*的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="67639-201">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="67639-202">*Pages/Index razor* （父组件）：</span><span class="sxs-lookup"><span data-stu-id="67639-202">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="67639-203">*Pages/Index. .cs*：</span><span class="sxs-lookup"><span data-stu-id="67639-203">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="67639-204">*Shared/SurveyPrompt* （子组件）：</span><span class="sxs-lookup"><span data-stu-id="67639-204">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="67639-205">*Shared/SurveyPrompt*：</span><span class="sxs-lookup"><span data-stu-id="67639-205">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="67639-206">从 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="67639-206">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="67639-207">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="67639-207">Static .NET method call</span></span>

<span data-ttu-id="67639-208">若要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="67639-208">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="67639-209">传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。</span><span class="sxs-lookup"><span data-stu-id="67639-209">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="67639-210">异步版本是支持 Blazor 服务器方案的首选。</span><span class="sxs-lookup"><span data-stu-id="67639-210">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="67639-211">.NET 方法必须是公共的，静态的，并具有 `[JSInvokable]` 特性。</span><span class="sxs-lookup"><span data-stu-id="67639-211">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="67639-212">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="67639-212">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="67639-213">示例应用包含一个方法C# ，该方法返回 `int`的数组。</span><span class="sxs-lookup"><span data-stu-id="67639-213">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="67639-214">`JSInvokable` 特性应用于方法。</span><span class="sxs-lookup"><span data-stu-id="67639-214">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="67639-215">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-215">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="67639-216">为客户端提供的 JavaScript 调用C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="67639-216">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="67639-217">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-217">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="67639-218">如果选择了 "**触发器 .net 静态方法 ReturnArrayAsync** " 按钮，请在浏览器的 web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="67639-218">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="67639-219">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="67639-219">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="67639-220">第四个数组值推送到 `ReturnArrayAsync`返回的数组（`data.push(4);`）。</span><span class="sxs-lookup"><span data-stu-id="67639-220">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="67639-221">默认情况下，方法标识符为方法名称，但可以使用 `JSInvokableAttribute` 构造函数指定其他标识符：</span><span class="sxs-lookup"><span data-stu-id="67639-221">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="67639-222">在客户端 JavaScript 文件中：</span><span class="sxs-lookup"><span data-stu-id="67639-222">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a><span data-ttu-id="67639-223">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="67639-223">Instance method call</span></span>

<span data-ttu-id="67639-224">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="67639-224">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="67639-225">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="67639-225">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="67639-226">通过引用向 JavaScript 传递 .NET 实例：</span><span class="sxs-lookup"><span data-stu-id="67639-226">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="67639-227">对 `DotNetObjectReference.Create`进行静态调用。</span><span class="sxs-lookup"><span data-stu-id="67639-227">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="67639-228">在 `DotNetObjectReference` 实例中包装实例，并在 `DotNetObjectReference` 实例上调用 `Create`。</span><span class="sxs-lookup"><span data-stu-id="67639-228">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="67639-229">释放 `DotNetObjectReference` 对象（本部分稍后会显示一个示例）。</span><span class="sxs-lookup"><span data-stu-id="67639-229">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="67639-230">使用 `invokeMethod` 或 `invokeMethodAsync` 函数调用实例上的 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="67639-230">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="67639-231">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="67639-231">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="67639-232">示例应用将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="67639-232">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="67639-233">对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="67639-233">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="67639-234">如果选择了 "**触发器 .net 实例方法 HelloHelper** " 按钮，则会调用 `ExampleJsInterop.CallHelloHelperSayHello` 并向方法传递 `Blazor`名称。</span><span class="sxs-lookup"><span data-stu-id="67639-234">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="67639-235">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-235">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="67639-236">`CallHelloHelperSayHello` 使用 `HelloHelper`的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="67639-236">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="67639-237">*JsInteropClasses/ExampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-237">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="67639-238">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="67639-238">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="67639-239">该名称将传递给 `HelloHelper`的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="67639-239">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="67639-240">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="67639-240">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="67639-241">*JsInteropClasses/HelloHelper*：</span><span class="sxs-lookup"><span data-stu-id="67639-241">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="67639-242">浏览器 web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="67639-242">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="67639-243">若要避免内存泄露，并允许对创建 `DotNetObjectReference`的组件进行垃圾回收，请释放类中创建 `DotNetObjectReference` 实例的对象：</span><span class="sxs-lookup"><span data-stu-id="67639-243">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

```csharp
public class ExampleJsInterop : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private DotNetObjectReference<HelloHelper> _objRef;

    public ExampleJsInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public ValueTask<string> CallHelloHelperSayHello(string name)
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper(name));

        return _jsRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```
  
<span data-ttu-id="67639-244">还可以在组件中实现前面 `ExampleJsInterop` 类中所示的模式：</span><span class="sxs-lookup"><span data-stu-id="67639-244">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
```razor
@page "/JSInteropComponent"
@using BlazorSample.JsInteropClasses
@implements IDisposable
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    private DotNetObjectReference<HelloHelper> _objRef;

    public async Task TriggerNetInstanceMethod()
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

        await JSRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="67639-245">在类库中共享互操作代码</span><span class="sxs-lookup"><span data-stu-id="67639-245">Share interop code in a class library</span></span>

<span data-ttu-id="67639-246">可在类库中包含 JS 互操作代码，这使你可以共享 NuGet 包中的代码。</span><span class="sxs-lookup"><span data-stu-id="67639-246">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="67639-247">类库处理在生成的程序集中嵌入 JavaScript 资源。</span><span class="sxs-lookup"><span data-stu-id="67639-247">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="67639-248">JavaScript 文件位于*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="67639-248">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="67639-249">工具在生成库时将负责嵌入资源。</span><span class="sxs-lookup"><span data-stu-id="67639-249">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="67639-250">在应用程序的项目文件中引用构建的 NuGet 包的方式与引用任何 NuGet 包的方式相同。</span><span class="sxs-lookup"><span data-stu-id="67639-250">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="67639-251">包还原后，应用程序代码可以调入 JavaScript，就像它是C#一样。</span><span class="sxs-lookup"><span data-stu-id="67639-251">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="67639-252">有关详细信息，请参阅 <xref:blazor/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="67639-252">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="67639-253">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="67639-253">Harden JS interop calls</span></span>

<span data-ttu-id="67639-254">由于网络错误，JS 互操作可能会失败，因此应将其视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="67639-254">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="67639-255">默认情况下，Blazor Server 应用程序一分钟后，服务器上的 JS 互操作调用会超时。</span><span class="sxs-lookup"><span data-stu-id="67639-255">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="67639-256">如果应用可以容忍更严格的超时，如10秒，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="67639-256">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="67639-257">在 `Startup.ConfigureServices`中全局指定超时值：</span><span class="sxs-lookup"><span data-stu-id="67639-257">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="67639-258">在组件代码中，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="67639-258">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="67639-259">有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="67639-259">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="67639-260">在 Blazor Server 应用中执行大型数据传输</span><span class="sxs-lookup"><span data-stu-id="67639-260">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="67639-261">在某些情况下，必须在 JavaScript 和 Blazor之间传输大量的数据。</span><span class="sxs-lookup"><span data-stu-id="67639-261">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="67639-262">通常，当发生以下情况时，会发生大型数据传输：</span><span class="sxs-lookup"><span data-stu-id="67639-262">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="67639-263">浏览器文件系统 Api 用于上传或下载文件。</span><span class="sxs-lookup"><span data-stu-id="67639-263">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="67639-264">需要使用第三方库的互操作。</span><span class="sxs-lookup"><span data-stu-id="67639-264">Interop with a third party library is required.</span></span>

<span data-ttu-id="67639-265">在 Blazor 服务器中，有一个限制是为了防止传递可能导致性能问题的单个大消息。</span><span class="sxs-lookup"><span data-stu-id="67639-265">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="67639-266">开发在 JavaScript 和 Blazor之间传输数据的代码时，请考虑以下指南：</span><span class="sxs-lookup"><span data-stu-id="67639-266">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="67639-267">将数据分割成较小的部分，并按顺序发送数据段，直到服务器接收到所有数据为止。</span><span class="sxs-lookup"><span data-stu-id="67639-267">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="67639-268">不要在 JavaScript 和C#代码中分配大型对象。</span><span class="sxs-lookup"><span data-stu-id="67639-268">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="67639-269">发送或接收数据时，请勿长时间阻止主 UI 线程。</span><span class="sxs-lookup"><span data-stu-id="67639-269">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="67639-270">释放完成或取消该进程时使用的任何内存。</span><span class="sxs-lookup"><span data-stu-id="67639-270">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="67639-271">出于安全目的，请强制执行以下附加要求：</span><span class="sxs-lookup"><span data-stu-id="67639-271">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="67639-272">声明可传递的最大文件或数据大小。</span><span class="sxs-lookup"><span data-stu-id="67639-272">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="67639-273">将客户端的最小上传速率声明到服务器。</span><span class="sxs-lookup"><span data-stu-id="67639-273">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="67639-274">服务器接收到数据后，可以使用以下数据：</span><span class="sxs-lookup"><span data-stu-id="67639-274">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="67639-275">临时存储在内存缓冲区中，直到收集所有段。</span><span class="sxs-lookup"><span data-stu-id="67639-275">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="67639-276">立即使用。</span><span class="sxs-lookup"><span data-stu-id="67639-276">Consumed immediately.</span></span> <span data-ttu-id="67639-277">例如，数据可以立即存储在数据库中，也可以在每个段被接收时写入磁盘。</span><span class="sxs-lookup"><span data-stu-id="67639-277">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="67639-278">以下文件上传者类通过客户端处理 JS 互操作。</span><span class="sxs-lookup"><span data-stu-id="67639-278">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="67639-279">上载者类使用 JS 互操作来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="67639-279">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="67639-280">轮询客户端以发送数据段。</span><span class="sxs-lookup"><span data-stu-id="67639-280">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="67639-281">如果轮询超时，则中止该事务。</span><span class="sxs-lookup"><span data-stu-id="67639-281">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="67639-282">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="67639-282">In the preceding example:</span></span>

* <span data-ttu-id="67639-283">`_maxBase64SegmentSize` 设置为 `8192`，它是从 `_maxBase64SegmentSize = _segmentSize * 4 / 3`计算得出的。</span><span class="sxs-lookup"><span data-stu-id="67639-283">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="67639-284">低级别 .NET Core 内存管理 Api 用于在 `_uploadedSegments`的服务器上存储内存段。</span><span class="sxs-lookup"><span data-stu-id="67639-284">Low level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="67639-285">`ReceiveFile` 方法用于通过 JS 互操作处理上载：</span><span class="sxs-lookup"><span data-stu-id="67639-285">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="67639-286">文件大小通过 `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`的 JS 互操作来确定。</span><span class="sxs-lookup"><span data-stu-id="67639-286">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="67639-287">要接收的段数将计算并存储在 `numberOfSegments`中。</span><span class="sxs-lookup"><span data-stu-id="67639-287">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="67639-288">使用 `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`通过 JS 互操作在 `for` 循环中请求段。</span><span class="sxs-lookup"><span data-stu-id="67639-288">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="67639-289">在解码之前，所有段都必须为8192字节。</span><span class="sxs-lookup"><span data-stu-id="67639-289">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="67639-290">客户端被迫以有效的方式发送数据。</span><span class="sxs-lookup"><span data-stu-id="67639-290">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="67639-291">对于收到的每个段，将在 <xref:System.Convert.TryFromBase64String*>解码之前执行检查。</span><span class="sxs-lookup"><span data-stu-id="67639-291">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="67639-292">上载完成后，包含数据的流将作为新的 <xref:System.IO.Stream> （`SegmentedStream`）返回。</span><span class="sxs-lookup"><span data-stu-id="67639-292">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="67639-293">分段流类将段列表作为 readonly 不可查找的 <xref:System.IO.Stream>公开：</span><span class="sxs-lookup"><span data-stu-id="67639-293">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="67639-294">以下代码实现了用于接收数据的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="67639-294">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

## <a name="additional-resources"></a><span data-ttu-id="67639-295">其他资源</span><span class="sxs-lookup"><span data-stu-id="67639-295">Additional resources</span></span>

* [<span data-ttu-id="67639-296">InteropComponent 示例（dotnet/AspNetCore GitHub 存储库，3.1 发布分支）</span><span class="sxs-lookup"><span data-stu-id="67639-296">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
