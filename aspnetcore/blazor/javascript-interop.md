---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor 应用中的 JavaScript 的 .NET 和 .NET 方法中调用 JavaScript 函数。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: 4edef123bc1fe41845b8060b9c3b8e77ffd2969d
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885467"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="bd4b1-103">ASP.NET Core Blazor JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="bd4b1-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="bd4b1-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd4b1-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bd4b1-105">Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="bd4b1-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bd4b1-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="bd4b1-107">从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="bd4b1-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="bd4b1-108">有时需要 .NET 代码才能调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="bd4b1-109">例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="bd4b1-110">此方案称为*JavaScript 互操作性*（*JS 互操作*）。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="bd4b1-111">若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="bd4b1-112">若要发出 JS 互操作调用，请在组件中注入 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="bd4b1-113">`InvokeAsync<T>` 方法采用 JavaScript 函数的标识符，该标识符将与任意数量的 JSON 可序列化参数一起调用。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="bd4b1-114">函数标识符相对于全局范围（`window`）。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="bd4b1-115">如果要调用 `window.someScope.someFunction`，则 `someScope.someFunction`标识符。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="bd4b1-116">无需在调用函数之前注册它。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="bd4b1-117">返回类型 `T` 也必须是 JSON 可序列化的。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="bd4b1-118">`T` 应匹配最适合于返回的 JSON 类型的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="bd4b1-119">对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="bd4b1-120">在建立与浏览器的连接后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="bd4b1-121">有关详细信息，请参阅[检测何时预呈现 Blazor 应用](#detect-when-a-blazor-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="bd4b1-122">下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="bd4b1-123">该示例演示如何从C#方法调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="bd4b1-124">JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="bd4b1-125">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="bd4b1-126">JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="bd4b1-127">以下组件：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-127">The following component:</span></span>

* <span data-ttu-id="bd4b1-128">选择组件按钮（**转换数组**）时使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="bd4b1-129">调用 JavaScript 函数后，传递的数组将转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="bd4b1-130">将字符串返回到要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="bd4b1-131">使用 IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="bd4b1-131">Use of IJSRuntime</span></span>

<span data-ttu-id="bd4b1-132">若要使用 `IJSRuntime` 抽象，请采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="bd4b1-133">将 `IJSRuntime` 抽象插入 Razor 组件（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="bd4b1-134">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="bd4b1-135">使用 `IJSRuntime.InvokeVoidAsync` 调用函数，并且不返回值：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="bd4b1-136">将 `IJSRuntime` 抽象插入类（ *.cs*）：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="bd4b1-137">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="bd4b1-138">使用 `JSRuntime.InvokeAsync` 调用函数并返回值：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="bd4b1-139">对于使用[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)的动态内容生成，请使用 `[Inject]` 特性：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="bd4b1-140">本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="bd4b1-141">`showPrompt` &ndash; 会生成一个提示，以接受用户输入（用户的名称），并将名称返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="bd4b1-142">`displayWelcome` &ndash; 将来自调用方的欢迎消息分配给具有 `welcome``id` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="bd4b1-143">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="bd4b1-144">将引用 JavaScript 文件的 `<script>` 标记放在*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/Blazor _Host*</span><span class="sxs-lookup"><span data-stu-id="bd4b1-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="bd4b1-145">*wwwroot/index.html* （Blazor WebAssembly）：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="bd4b1-146">*Pages/_Host cshtml* （Blazor Server）：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="bd4b1-147">请勿在组件文件中放置 `<script>` 标记，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="bd4b1-148">通过调用 `IJSRuntime.InvokeAsync<T>`，.NET 方法与*exampleJsInterop*文件中的 JavaScript 函数互操作。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="bd4b1-149">`IJSRuntime` 抽象是异步的，以允许 Blazor 服务器方案。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="bd4b1-150">如果应用是 Blazor WebAssembly 应用，并且希望同步调用 JavaScript 函数，则向下转换到 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="bd4b1-151">建议大多数 JS 互操作库使用异步 Api，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="bd4b1-152">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="bd4b1-153">组件：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-153">The component:</span></span>

* <span data-ttu-id="bd4b1-154">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="bd4b1-155">将文本返回到组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="bd4b1-156">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="bd4b1-157">*Pages/JSInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-157">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="bd4b1-158">通过选择组件的 "**触发器 JavaScript" 提示**按钮执行 `TriggerJsPrompt` 时，将调用*wwwroot/exampleJsInterop*文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="bd4b1-159">`showPrompt` 函数接受 HTML 编码并返回到组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="bd4b1-160">组件将用户的名称存储在本地变量 `name`中。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="bd4b1-161">存储在 `name` 中的字符串合并为一个欢迎消息，该消息将被传递给 JavaScript 函数，`displayWelcome`，该消息会将欢迎消息呈现到标题标记中。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="bd4b1-162">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="bd4b1-162">Call a void JavaScript function</span></span>

<span data-ttu-id="bd4b1-163">返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数通过 `IJSRuntime.InvokeVoidAsync`调用。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="bd4b1-164">检测何时预呈现 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="bd4b1-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="bd4b1-165">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="bd4b1-165">Capture references to elements</span></span>

<span data-ttu-id="bd4b1-166">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="bd4b1-167">例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素上调用类似命令的 Api，如 `focus` 或 `play`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="bd4b1-168">使用以下方法捕获对组件中 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="bd4b1-169">将 `@ref` 特性添加到 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="bd4b1-170">定义一个类型 `ElementReference` 字段，其名称与 `@ref` 属性的值相匹配。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="bd4b1-171">下面的示例演示捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="bd4b1-172">只使用元素引用来改变不与 Blazor进行交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="bd4b1-173">当第三方 API 向元素提供内容时，此方案很有用。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="bd4b1-174">由于 Blazor 不与元素交互，因此在 Blazor的元素和 DOM 的表示形式之间不存在冲突。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="bd4b1-175">在下面的示例中，改变无序列表（`ul`）的内容很*危险*，因为 Blazor 与 DOM 交互以填充此元素的列表项（`<li>`）：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="bd4b1-176">如果 JS 互操作改变了元素 `MyList` 的内容，并且 Blazor 尝试将差异应用到元素，则差异将与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="bd4b1-177">就 .NET 代码而言，`ElementReference` 是一种不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="bd4b1-178">使用 `ElementReference` 可以执行的*唯一*操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="bd4b1-179">当你执行此操作时，JavaScript 端代码将收到一个 `HTMLElement` 实例，该实例可用于普通 DOM Api。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="bd4b1-180">例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="bd4b1-181">*exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="bd4b1-182">若要调用不返回值的 JavaScript 函数，请使用 `IJSRuntime.InvokeVoidAsync`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="bd4b1-183">下面的代码通过使用捕获的 `ElementReference`调用前面的 JavaScript 函数，对用户名输入设置焦点：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="bd4b1-184">若要使用扩展方法，请创建一个静态扩展方法，用于接收 `IJSRuntime` 实例：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="bd4b1-185">在对象上直接调用 `Focus` 方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="bd4b1-186">下面的示例假定 `Focus` 方法可从 `JsInteropClasses` 命名空间中获得：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="bd4b1-187">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="bd4b1-188">如果将未填充 `ElementReference` 传递给 JavaScript 代码，JavaScript 代码将接收值 `null`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="bd4b1-189">若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用[OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="bd4b1-190">使用泛型类型并返回值时，请使用[ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1)：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="bd4b1-191">直接在具有类型的对象上调用 `GenericMethod`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="bd4b1-192">下面的示例假定 `JsInteropClasses` 命名空间中提供了 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="bd4b1-193">从 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="bd4b1-193">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="bd4b1-194">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="bd4b1-194">Static .NET method call</span></span>

<span data-ttu-id="bd4b1-195">若要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-195">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="bd4b1-196">传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-196">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="bd4b1-197">异步版本是支持 Blazor 服务器方案的首选。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-197">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="bd4b1-198">若要从 JavaScript 调用 .NET 方法，.NET 方法必须是公共的、静态的并且具有 `[JSInvokable]` 特性。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-198">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="bd4b1-199">默认情况下，方法标识符为方法名称，但可以使用 `JSInvokableAttribute` 构造函数指定其他标识符。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-199">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="bd4b1-200">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-200">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="bd4b1-201">示例应用包含一个方法C# ，该方法返回 `int`的数组。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-201">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="bd4b1-202">`JSInvokable` 特性应用于方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-202">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="bd4b1-203">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-203">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="bd4b1-204">为客户端提供的 JavaScript 调用C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-204">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="bd4b1-205">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-205">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="bd4b1-206">如果选择了 "**触发器 .net 静态方法 ReturnArrayAsync** " 按钮，请在浏览器的 web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-206">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="bd4b1-207">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-207">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="bd4b1-208">第四个数组值推送到 `ReturnArrayAsync`返回的数组（`data.push(4);`）。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-208">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="bd4b1-209">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="bd4b1-209">Instance method call</span></span>

<span data-ttu-id="bd4b1-210">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-210">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="bd4b1-211">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-211">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="bd4b1-212">通过在 `DotNetObjectReference` 实例中包装 .NET 实例，将其传递给 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-212">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="bd4b1-213">.NET 实例按对 JavaScript 的引用传递。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-213">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="bd4b1-214">使用 `invokeMethod` 或 `invokeMethodAsync` 函数调用实例上的 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-214">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="bd4b1-215">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-215">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="bd4b1-216">示例应用将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-216">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="bd4b1-217">对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-217">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="bd4b1-218">如果选择了 "**触发器 .net 实例方法 HelloHelper** " 按钮，则会调用 `ExampleJsInterop.CallHelloHelperSayHello` 并向方法传递 `Blazor`名称。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-218">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="bd4b1-219">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-219">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="bd4b1-220">`CallHelloHelperSayHello` 使用 `HelloHelper`的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-220">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="bd4b1-221">*JsInteropClasses/ExampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-221">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="bd4b1-222">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-222">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="bd4b1-223">该名称将传递给 `HelloHelper`的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-223">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="bd4b1-224">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-224">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="bd4b1-225">*JsInteropClasses/HelloHelper*：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-225">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="bd4b1-226">浏览器 web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-226">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="bd4b1-227">在类库中共享互操作代码</span><span class="sxs-lookup"><span data-stu-id="bd4b1-227">Share interop code in a class library</span></span>

<span data-ttu-id="bd4b1-228">可在类库中包含 JS 互操作代码，这使你可以共享 NuGet 包中的代码。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-228">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="bd4b1-229">类库处理在生成的程序集中嵌入 JavaScript 资源。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-229">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="bd4b1-230">JavaScript 文件位于*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-230">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="bd4b1-231">工具在生成库时将负责嵌入资源。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-231">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="bd4b1-232">在应用程序的项目文件中引用构建的 NuGet 包的方式与引用任何 NuGet 包的方式相同。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-232">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="bd4b1-233">包还原后，应用程序代码可以调入 JavaScript，就像它是C#一样。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-233">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="bd4b1-234">有关更多信息，请参见<xref:blazor/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-234">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="bd4b1-235">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="bd4b1-235">Harden JS interop calls</span></span>

<span data-ttu-id="bd4b1-236">由于网络错误，JS 互操作可能会失败，因此应将其视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-236">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="bd4b1-237">默认情况下，Blazor Server 应用程序一分钟后，服务器上的 JS 互操作调用会超时。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-237">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="bd4b1-238">如果应用可以容忍更严格的超时，如10秒，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-238">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="bd4b1-239">在 `Startup.ConfigureServices`中全局指定超时值：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-239">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="bd4b1-240">在组件代码中，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="bd4b1-240">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="bd4b1-241">有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="bd4b1-241">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd4b1-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd4b1-242">Additional resources</span></span>

* [<span data-ttu-id="bd4b1-243">InteropComponent 示例（dotnet/AspNetCore GitHub 存储库，3.1 发布分支）</span><span class="sxs-lookup"><span data-stu-id="bd4b1-243">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
