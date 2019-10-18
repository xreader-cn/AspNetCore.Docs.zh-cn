---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor apps 中的 JavaScript 调用来自 .NET 和 .NET 方法的 JavaScript 函数。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/javascript-interop
ms.openlocfilehash: a8c3a0951761faab1c11507834aeef2507388d71
ms.sourcegitcommit: ce2bfb01f2cc7dd83f8a97da0689d232c71bcdc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531124"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="1153e-103">ASP.NET Core Blazor JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="1153e-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="1153e-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1153e-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1153e-105">Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="1153e-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1153e-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="1153e-107">从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="1153e-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="1153e-108">有时需要 .NET 代码才能调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="1153e-109">例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。</span><span class="sxs-lookup"><span data-stu-id="1153e-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="1153e-110">若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="1153e-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="1153e-111">@No__t-0 方法获取要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="1153e-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="1153e-112">函数标识符相对于全局范围（`window`）。</span><span class="sxs-lookup"><span data-stu-id="1153e-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="1153e-113">如果要调用 `window.someScope.someFunction`，标识符 @no__t 为-1。</span><span class="sxs-lookup"><span data-stu-id="1153e-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="1153e-114">无需在调用函数之前注册它。</span><span class="sxs-lookup"><span data-stu-id="1153e-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="1153e-115">返回类型 `T` 也必须是 JSON 可序列化的。</span><span class="sxs-lookup"><span data-stu-id="1153e-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="1153e-116">对于 Blazor 服务器应用：</span><span class="sxs-lookup"><span data-stu-id="1153e-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="1153e-117">Blazor 服务器应用处理多个用户请求。</span><span class="sxs-lookup"><span data-stu-id="1153e-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="1153e-118">请勿在组件中调用 `JSRuntime.Current` 来调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="1153e-119">注入 `IJSRuntime` 抽象，并使用注入的对象发出 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="1153e-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="1153e-120">预呈现 Blazor 的应用时，无法调用 JavaScript，因为尚未建立与浏览器的连接。</span><span class="sxs-lookup"><span data-stu-id="1153e-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="1153e-121">有关详细信息，请参阅 "在预[呈现 Blazor 应用时检测](#detect-when-a-blazor-app-is-prerendering)" 部分。</span><span class="sxs-lookup"><span data-stu-id="1153e-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="1153e-122">下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="1153e-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="1153e-123">该示例演示如何从C#方法调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="1153e-124">JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。</span><span class="sxs-lookup"><span data-stu-id="1153e-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="1153e-125">在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor 服务器）的 `<head>` 元素中，提供一个函数，该函数使用 `TextDecoder` 对传递的数组进行解码：</span><span class="sxs-lookup"><span data-stu-id="1153e-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="1153e-126">JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：</span><span class="sxs-lookup"><span data-stu-id="1153e-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="1153e-127">以下组件：</span><span class="sxs-lookup"><span data-stu-id="1153e-127">The following component:</span></span>

* <span data-ttu-id="1153e-128">选择组件按钮（**转换数组**）时，使用 `JsRuntime` 调用 `ConvertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="1153e-129">调用 JavaScript 函数后，传递的数组将转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="1153e-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="1153e-130">将字符串返回到要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="1153e-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="1153e-131">若要使用 `IJSRuntime` 抽象，请采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="1153e-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="1153e-132">将 `IJSRuntime` 抽象插入 Razor 组件（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="1153e-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="1153e-133">将 `IJSRuntime` 抽象插入到类（ *.cs*）中：</span><span class="sxs-lookup"><span data-stu-id="1153e-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="1153e-134">对于使用[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)的动态内容生成，请使用 `[Inject]` 特性：</span><span class="sxs-lookup"><span data-stu-id="1153e-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="1153e-135">本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="1153e-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="1153e-136">`showPrompt` &ndash; 将生成一个接收用户输入（用户的名称）的提示，并将名称返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="1153e-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="1153e-137">`displayWelcome` &ndash; 将一个欢迎消息从调用方分配到 @no__t 为 `id` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="1153e-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="1153e-138">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="1153e-139">将引用 JavaScript 文件的 `<script>` 标记放置在*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/_Host*文件（Blazor 服务器）中。</span><span class="sxs-lookup"><span data-stu-id="1153e-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="1153e-140">*wwwroot/index.html* （Blazor WebAssembly）：</span><span class="sxs-lookup"><span data-stu-id="1153e-140">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="1153e-141">*Pages/_Host* （Blazor 服务器）：</span><span class="sxs-lookup"><span data-stu-id="1153e-141">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="1153e-142">请勿在组件文件中放置 @no__t 的标记，因为 @no__t 的标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="1153e-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="1153e-143">.NET 方法通过调用 `IJSRuntime.InvokeAsync<T>`，与*exampleJsInterop*文件中的 JavaScript 函数互操作。</span><span class="sxs-lookup"><span data-stu-id="1153e-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="1153e-144">@No__t-0 抽象是异步的，以允许 Blazor 服务器方案。</span><span class="sxs-lookup"><span data-stu-id="1153e-144">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="1153e-145">如果应用是 Blazor WebAssembly 应用，并且你想要同步调用 JavaScript 函数，则向下转换到 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。</span><span class="sxs-lookup"><span data-stu-id="1153e-145">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="1153e-146">建议大多数 JavaScript 互操作库使用异步 Api，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="1153e-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="1153e-147">该示例应用包含一个用于演示 JavaScript 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="1153e-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="1153e-148">组件：</span><span class="sxs-lookup"><span data-stu-id="1153e-148">The component:</span></span>

* <span data-ttu-id="1153e-149">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="1153e-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="1153e-150">将文本返回到组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="1153e-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="1153e-151">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="1153e-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="1153e-152">*Pages/JSInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="1153e-153">通过选择组件的 "**触发器 JavaScript" 提示**按钮执行 `TriggerJsPrompt` 时，将调用*wwwroot/exampleJsInterop*文件中提供的 JavaScript `showPrompt` 功能。</span><span class="sxs-lookup"><span data-stu-id="1153e-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="1153e-154">@No__t-0 函数接受经过 HTML 编码并返回到组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="1153e-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="1153e-155">组件将用户的名称存储在本地变量中，`name`。</span><span class="sxs-lookup"><span data-stu-id="1153e-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="1153e-156">存储在 `name` 中的字符串合并为一个欢迎消息，该消息将被传递给 JavaScript 函数，`displayWelcome`，这会将欢迎消息呈现到标题标记中。</span><span class="sxs-lookup"><span data-stu-id="1153e-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="1153e-157">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="1153e-157">Call a void JavaScript function</span></span>

<span data-ttu-id="1153e-158">返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数在 `IJSRuntime.InvokeVoidAsync` 的情况中调用。</span><span class="sxs-lookup"><span data-stu-id="1153e-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="1153e-159">检测何时预呈现 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="1153e-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="1153e-160">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="1153e-160">Capture references to elements</span></span>

<span data-ttu-id="1153e-161">某些[JavaScript 互操作](xref:blazor/javascript-interop)方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="1153e-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="1153e-162">例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素（例如 `focus` 或 `play`）上调用类似命令的 Api。</span><span class="sxs-lookup"><span data-stu-id="1153e-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="1153e-163">使用以下方法捕获对组件中 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="1153e-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="1153e-164">将 `@ref` 特性添加到 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="1153e-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="1153e-165">定义类型 `ElementReference` 的字段，其名称与 @no__t 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="1153e-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="1153e-166">下面的示例演示捕获对 `username` @no__t 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="1153e-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="1153e-167">不要**使用捕获**的元素引用作为填充 DOM 的方式。</span><span class="sxs-lookup"><span data-stu-id="1153e-167">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="1153e-168">这样做可能会干扰声明性呈现模型。</span><span class="sxs-lookup"><span data-stu-id="1153e-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="1153e-169">就 .NET 代码而言，@no__t 0 是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="1153e-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="1153e-170">使用 `ElementReference` 可以执行的*唯一*操作是通过 javascript 互操作将它传递给 javascript 代码。</span><span class="sxs-lookup"><span data-stu-id="1153e-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="1153e-171">当你执行此操作时，JavaScript 端代码将收到一个 @no__t 为0的实例，该实例可用于普通 DOM Api。</span><span class="sxs-lookup"><span data-stu-id="1153e-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="1153e-172">例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="1153e-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="1153e-173">*exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="1153e-174">使用 `IJSRuntime.InvokeAsync<T>` 并使用 `ElementReference` 调用 @no__t 以使元素聚焦：</span><span class="sxs-lookup"><span data-stu-id="1153e-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="1153e-175">若要使用扩展方法集中元素，请创建一个静态扩展方法，用于接收 @no__t 实例：</span><span class="sxs-lookup"><span data-stu-id="1153e-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="1153e-176">在对象上直接调用方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-176">The method is called directly on the object.</span></span> <span data-ttu-id="1153e-177">下面的示例假定 @no__t 命名空间中提供了 static `Focus` 方法：</span><span class="sxs-lookup"><span data-stu-id="1153e-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="1153e-178">仅在呈现组件后，才填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="1153e-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="1153e-179">如果向 JavaScript 代码传递了未填充 `ElementReference`，JavaScript 代码将接收值 `null`。</span><span class="sxs-lookup"><span data-stu-id="1153e-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="1153e-180">若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用 @no__t 0 或 @no__t[组件生命周期方法](xref:blazor/components#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="1153e-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="1153e-181">从 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="1153e-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="1153e-182">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="1153e-182">Static .NET method call</span></span>

<span data-ttu-id="1153e-183">若要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="1153e-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="1153e-184">传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。</span><span class="sxs-lookup"><span data-stu-id="1153e-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="1153e-185">异步版本是支持 Blazor 服务器方案的首选。</span><span class="sxs-lookup"><span data-stu-id="1153e-185">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="1153e-186">若要从 JavaScript 调用 .NET 方法，.NET 方法必须是公共的、静态的并且具有 `[JSInvokable]` 特性。</span><span class="sxs-lookup"><span data-stu-id="1153e-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="1153e-187">默认情况下，方法标识符为方法名称，但可以使用 `JSInvokableAttribute` 构造函数指定其他标识符。</span><span class="sxs-lookup"><span data-stu-id="1153e-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="1153e-188">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="1153e-189">示例应用包含一个方法C# ，该方法返回 @no__t 1 的数组。</span><span class="sxs-lookup"><span data-stu-id="1153e-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="1153e-190">@No__t-0 特性应用于方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="1153e-191">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="1153e-192">为客户端提供的 JavaScript 调用C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="1153e-193">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="1153e-194">如果选择了 "**触发器 .net 静态方法 ReturnArrayAsync** " 按钮，请在浏览器的 web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="1153e-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="1153e-195">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="1153e-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="1153e-196">第四个数组值推送到 @no__t 返回的数组（@no__t）。</span><span class="sxs-lookup"><span data-stu-id="1153e-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="1153e-197">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="1153e-197">Instance method call</span></span>

<span data-ttu-id="1153e-198">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="1153e-199">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="1153e-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="1153e-200">通过在 `DotNetObjectReference` 实例中包装 .NET 实例，将其传递给 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="1153e-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="1153e-201">.NET 实例按对 JavaScript 的引用传递。</span><span class="sxs-lookup"><span data-stu-id="1153e-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="1153e-202">使用 @no__t 0 或 `invokeMethodAsync` 函数调用实例上的 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="1153e-203">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="1153e-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="1153e-204">示例应用将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="1153e-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="1153e-205">对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="1153e-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="1153e-206">如果选择了**触发器 .net 实例方法 HelloHelper SayHello**按钮，则会调用 `ExampleJsInterop.CallHelloHelperSayHello`，并将名称 `Blazor` 传递给方法。</span><span class="sxs-lookup"><span data-stu-id="1153e-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="1153e-207">*Pages/JsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="1153e-208">`CallHelloHelperSayHello` 用 `HelloHelper` 的新实例调用 JavaScript 函数 @no__t。</span><span class="sxs-lookup"><span data-stu-id="1153e-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="1153e-209">*JsInteropClasses/ExampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="1153e-210">*wwwroot/exampleJsInterop*：</span><span class="sxs-lookup"><span data-stu-id="1153e-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="1153e-211">该名称将传递给 @no__t 0 的构造函数，该构造函数设置 @no__t 的属性。</span><span class="sxs-lookup"><span data-stu-id="1153e-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="1153e-212">当执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="1153e-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="1153e-213">*JsInteropClasses/HelloHelper*：</span><span class="sxs-lookup"><span data-stu-id="1153e-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="1153e-214">浏览器 web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="1153e-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="1153e-215">在类库中共享互操作代码</span><span class="sxs-lookup"><span data-stu-id="1153e-215">Share interop code in a class library</span></span>

<span data-ttu-id="1153e-216">JavaScript 互操作代码可以包含在类库中，这使你可以共享 NuGet 包中的代码。</span><span class="sxs-lookup"><span data-stu-id="1153e-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="1153e-217">类库处理在生成的程序集中嵌入 JavaScript 资源。</span><span class="sxs-lookup"><span data-stu-id="1153e-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="1153e-218">JavaScript 文件位于*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1153e-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="1153e-219">工具在生成库时将负责嵌入资源。</span><span class="sxs-lookup"><span data-stu-id="1153e-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="1153e-220">在应用程序的项目文件中引用构建的 NuGet 包的方式与引用任何 NuGet 包的方式相同。</span><span class="sxs-lookup"><span data-stu-id="1153e-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="1153e-221">包还原后，应用程序代码可以调入 JavaScript，就像它是C#一样。</span><span class="sxs-lookup"><span data-stu-id="1153e-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="1153e-222">有关更多信息，请参见<xref:blazor/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="1153e-222">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="1153e-223">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="1153e-223">Harden JS interop calls</span></span>

<span data-ttu-id="1153e-224">由于网络错误，JS 互操作可能会失败，因此应将其视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="1153e-224">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="1153e-225">默认情况下，Blazor 服务器应用程序在一分钟后就会超时服务器上的 JS 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="1153e-225">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="1153e-226">如果应用可以容忍更严格的超时，如10秒，请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="1153e-226">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="1153e-227">在 @no__t 中全局地指定超时：</span><span class="sxs-lookup"><span data-stu-id="1153e-227">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="1153e-228">在组件代码中，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="1153e-228">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="1153e-229">有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="1153e-229">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
