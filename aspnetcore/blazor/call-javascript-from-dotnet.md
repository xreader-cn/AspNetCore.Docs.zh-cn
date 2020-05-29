---
<span data-ttu-id="de453-101">title:“在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数”author: description:“了解如何在 Blazor 应用中从 .NET 方法调用 JavaScript 函数。”</span><span class="sxs-lookup"><span data-stu-id="de453-101">title: 'Call JavaScript functions from .NET methods in ASP.NET Core Blazor' author: description: 'Learn how to invoke JavaScript functions from .NET methods in Blazor apps.'</span></span>
<span data-ttu-id="de453-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="de453-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="de453-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="de453-103">'Blazor'</span></span>
- <span data-ttu-id="de453-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="de453-104">'Identity'</span></span>
- <span data-ttu-id="de453-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="de453-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="de453-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="de453-106">'Razor'</span></span>
- <span data-ttu-id="de453-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="de453-107">'SignalR' uid:</span></span> 

---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="de453-108">在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="de453-108">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="de453-109">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de453-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="de453-110">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="de453-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="de453-111">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="de453-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="de453-112">本文介绍如何从 .NET 调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-112">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="de453-113">有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。</span><span class="sxs-lookup"><span data-stu-id="de453-113">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="de453-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="de453-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="de453-115">若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="de453-115">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="de453-116">若要发出 JS 互操作调用，请在组件中注入 `IJSRuntime` 抽象。</span><span class="sxs-lookup"><span data-stu-id="de453-116">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="de453-117">`InvokeAsync<T>` 方法采用要与任意数量的 JSON 可序列化参数一起调用的 JavaScript 函数的标识符。</span><span class="sxs-lookup"><span data-stu-id="de453-117">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="de453-118">函数标识符相对于全局范围 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="de453-118">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="de453-119">如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="de453-119">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="de453-120">无需在调用函数之前进行注册。</span><span class="sxs-lookup"><span data-stu-id="de453-120">There's no need to register the function before it's called.</span></span> <span data-ttu-id="de453-121">返回类型 `T` 也必须可进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="de453-121">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="de453-122">`T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="de453-122">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="de453-123">对于启用了预呈现的 Blazor 服务器应用，初始预呈现期间无法调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="de453-123">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="de453-124">在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="de453-124">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="de453-125">有关详细信息，请参阅[检测 Blazor 服务器应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="de453-125">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="de453-126">下面的示例基于 [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。</span><span class="sxs-lookup"><span data-stu-id="de453-126">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="de453-127">该示例演示如何从 C# 方法调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-127">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="de453-128">JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="de453-128">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="de453-129">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：</span><span class="sxs-lookup"><span data-stu-id="de453-129">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="de453-130">JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (.js) 加载：</span><span class="sxs-lookup"><span data-stu-id="de453-130">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="de453-131">以下组件：</span><span class="sxs-lookup"><span data-stu-id="de453-131">The following component:</span></span>

* <span data-ttu-id="de453-132">在选择了组件按钮（“转换数组”）时使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-132">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="de453-133">调用 JavaScript 函数之后，传递的数组会转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="de453-133">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="de453-134">该字符串会返回给组件进行显示。</span><span class="sxs-lookup"><span data-stu-id="de453-134">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="de453-135">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="de453-135">IJSRuntime</span></span>

<span data-ttu-id="de453-136">若要使用 `IJSRuntime` 抽象，请采用以下任何方法：</span><span class="sxs-lookup"><span data-stu-id="de453-136">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="de453-137">将 `IJSRuntime` 抽象注入 Razor 组件 (.razor) 中：</span><span class="sxs-lookup"><span data-stu-id="de453-137">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="de453-138">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-138">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="de453-139">该函数通过 `IJSRuntime.InvokeVoidAsync` 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="de453-139">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="de453-140">将 `IJSRuntime` 抽象注入一个类 (.cs)：</span><span class="sxs-lookup"><span data-stu-id="de453-140">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="de453-141">在 wwwroot/index.html (Blazor WebAssembly) 或 Pages/_Host.cshtml（Blazor 服务器）的 `<head>` 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-141">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="de453-142">该函数通过 `JSRuntime.InvokeAsync` 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="de453-142">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="de453-143">对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：</span><span class="sxs-lookup"><span data-stu-id="de453-143">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="de453-144">在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：</span><span class="sxs-lookup"><span data-stu-id="de453-144">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="de453-145">`showPrompt` &ndash; 生成一个提示，以接受用户输入（用户名）并将名称返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="de453-145">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="de453-146">`displayWelcome` &ndash; 将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。</span><span class="sxs-lookup"><span data-stu-id="de453-146">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="de453-147">wwwroot/exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="de453-147">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="de453-148">将引用 JavaScript 文件的 `<script>` 标记置于 wwwroot/index.html 文件 (Blazor WebAssembly) 或 Pages/_Host.cshtml 文件（Blazor 服务器）中。</span><span class="sxs-lookup"><span data-stu-id="de453-148">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="de453-149">wwwroot/index.html (Blazor WebAssembly)：</span><span class="sxs-lookup"><span data-stu-id="de453-149">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="de453-150">Pages/_Host.cshtml（Blazor 服务器）：</span><span class="sxs-lookup"><span data-stu-id="de453-150">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="de453-151">请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。</span><span class="sxs-lookup"><span data-stu-id="de453-151">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="de453-152">.NET 方法通过调用 `IJSRuntime.InvokeAsync<T>` 与 exampleJsInterop.js 文件中的 JavaScript 函数进行互操作。</span><span class="sxs-lookup"><span data-stu-id="de453-152">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="de453-153">`IJSRuntime` 抽象是异步的，以便可以实现 Blazor 服务器方案。</span><span class="sxs-lookup"><span data-stu-id="de453-153">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="de453-154">如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。</span><span class="sxs-lookup"><span data-stu-id="de453-154">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="de453-155">建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。</span><span class="sxs-lookup"><span data-stu-id="de453-155">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="de453-156">该示例应用包含一个用于演示 JS 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="de453-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="de453-157">该组件：</span><span class="sxs-lookup"><span data-stu-id="de453-157">The component:</span></span>

* <span data-ttu-id="de453-158">通过 JavaScript 提示接收用户输入。</span><span class="sxs-lookup"><span data-stu-id="de453-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="de453-159">将文本返回给组件进行处理。</span><span class="sxs-lookup"><span data-stu-id="de453-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="de453-160">调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="de453-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="de453-161">Pages/JSInterop.razor：</span><span class="sxs-lookup"><span data-stu-id="de453-161">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="de453-162">通过选择组件的“触发 JavaScript 提示符”按钮来执行 `TriggerJsPrompt` 时，则会调用在 wwwroot/exampleJsInterop.js 文件中提供的 JavaScript `showPrompt` 函数。</span><span class="sxs-lookup"><span data-stu-id="de453-162">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="de453-163">`showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。</span><span class="sxs-lookup"><span data-stu-id="de453-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="de453-164">组件将用户的名称存储在本地变量 `name` 中。</span><span class="sxs-lookup"><span data-stu-id="de453-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="de453-165">存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。</span><span class="sxs-lookup"><span data-stu-id="de453-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="de453-166">调用 void JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="de453-166">Call a void JavaScript function</span></span>

<span data-ttu-id="de453-167">返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数使用 `IJSRuntime.InvokeVoidAsync` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="de453-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="de453-168">检测 Blazor 服务器应用进行预呈现的时间</span><span class="sxs-lookup"><span data-stu-id="de453-168">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="de453-169">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="de453-169">Capture references to elements</span></span>

<span data-ttu-id="de453-170">某些 JS 互操作方案需要引用 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="de453-170">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="de453-171">例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。</span><span class="sxs-lookup"><span data-stu-id="de453-171">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="de453-172">使用以下方法在组件中捕获对 HTML 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="de453-172">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="de453-173">向 HTML 元素添加 `@ref` 属性。</span><span class="sxs-lookup"><span data-stu-id="de453-173">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="de453-174">定义一个类型为 `ElementReference` 字段，其名称与 `@ref` 属性的值匹配。</span><span class="sxs-lookup"><span data-stu-id="de453-174">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="de453-175">以下示例演示如何捕获对 `username` `<input>` 元素的引用：</span><span class="sxs-lookup"><span data-stu-id="de453-175">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="de453-176">只使用元素引用改变不与 Blazor 交互的空元素的内容。</span><span class="sxs-lookup"><span data-stu-id="de453-176">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="de453-177">当第三方 API 向元素提供内容时，此方案十分有用。</span><span class="sxs-lookup"><span data-stu-id="de453-177">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="de453-178">由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。</span><span class="sxs-lookup"><span data-stu-id="de453-178">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="de453-179">在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：</span><span class="sxs-lookup"><span data-stu-id="de453-179">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="de453-180">如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。</span><span class="sxs-lookup"><span data-stu-id="de453-180">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="de453-181">就 .NET 代码而言，`ElementReference` 是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="de453-181">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="de453-182">可以对 `ElementReference` 执行的唯一操作是通过 JS 互操作将它传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="de453-182">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="de453-183">执行此操作时，JavaScript 端代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。</span><span class="sxs-lookup"><span data-stu-id="de453-183">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="de453-184">例如，以下代码定义一个 .NET 扩展方法，通过该方法可在元素上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="de453-184">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="de453-185">exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="de453-185">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="de453-186">若要调用不返回值的 JavaScript 函数，请使用 `IJSRuntime.InvokeVoidAsync`。</span><span class="sxs-lookup"><span data-stu-id="de453-186">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="de453-187">下面的代码通过使用捕获的 `ElementReference` 调用前面的 JavaScript 函数，在用户名输入上设置焦点：</span><span class="sxs-lookup"><span data-stu-id="de453-187">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="de453-188">若要使用扩展方法，请创建接收 `IJSRuntime` 实例的静态扩展方法：</span><span class="sxs-lookup"><span data-stu-id="de453-188">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="de453-189">`Focus` 方法在对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="de453-189">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="de453-190">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `Focus` 方法：</span><span class="sxs-lookup"><span data-stu-id="de453-190">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="de453-191">仅在呈现组件后填充 `username` 变量。</span><span class="sxs-lookup"><span data-stu-id="de453-191">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="de453-192">如果将未填充的 `ElementReference` 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="de453-192">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="de453-193">若要在组件完成呈现之后操作元素引用（用于对元素设置初始焦点），请使用 [OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="de453-193">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="de453-194">使用泛型类型并返回值时，请使用 [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1)：</span><span class="sxs-lookup"><span data-stu-id="de453-194">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="de453-195">`GenericMethod` 在具有类型的对象上直接调用。</span><span class="sxs-lookup"><span data-stu-id="de453-195">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="de453-196">下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：</span><span class="sxs-lookup"><span data-stu-id="de453-196">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="de453-197">跨组件引用元素</span><span class="sxs-lookup"><span data-stu-id="de453-197">Reference elements across components</span></span>

<span data-ttu-id="de453-198">`ElementReference` 仅保证在组件的 `OnAfterRender` 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。</span><span class="sxs-lookup"><span data-stu-id="de453-198">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="de453-199">若要使父组件可以向其他组件提供元素引用，父组件可以：</span><span class="sxs-lookup"><span data-stu-id="de453-199">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="de453-200">允许子组件注册回调。</span><span class="sxs-lookup"><span data-stu-id="de453-200">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="de453-201">在 `OnAfterRender` 事件期间，通过传递的元素引用调用注册的回调。</span><span class="sxs-lookup"><span data-stu-id="de453-201">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="de453-202">此方法间接地允许子组件与父级的元素引用交互。</span><span class="sxs-lookup"><span data-stu-id="de453-202">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="de453-203">以下 Blazor WebAssembly 示例演示了该方法。</span><span class="sxs-lookup"><span data-stu-id="de453-203">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="de453-204">在 wwwroot/index.html 的 `<head>` 中：</span><span class="sxs-lookup"><span data-stu-id="de453-204">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="de453-205">在 wwwroot/index.html 的 `<body>` 中：</span><span class="sxs-lookup"><span data-stu-id="de453-205">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="de453-206">Pages/Index.razor（父组件）：</span><span class="sxs-lookup"><span data-stu-id="de453-206">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="de453-207">Pages/Index.razor.cs：</span><span class="sxs-lookup"><span data-stu-id="de453-207">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="de453-208">Shared/SurveyPrompt.razor（子组件）：</span><span class="sxs-lookup"><span data-stu-id="de453-208">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="de453-209">Shared/SurveyPrompt.razor.cs：</span><span class="sxs-lookup"><span data-stu-id="de453-209">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="de453-210">强化 JS 互操作调用</span><span class="sxs-lookup"><span data-stu-id="de453-210">Harden JS interop calls</span></span>

<span data-ttu-id="de453-211">JS 互操作可能会由于网络错误而失败，因此应视为不可靠。</span><span class="sxs-lookup"><span data-stu-id="de453-211">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="de453-212">默认情况下，Blazor 服务器应用会在一分钟后，使服务器上的 JS 互操作调用超时。</span><span class="sxs-lookup"><span data-stu-id="de453-212">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="de453-213">如果应用可以容忍更严格的超时（如 10秒），请使用以下方法之一设置超时：</span><span class="sxs-lookup"><span data-stu-id="de453-213">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="de453-214">在 `Startup.ConfigureServices` 中全局指定超时：</span><span class="sxs-lookup"><span data-stu-id="de453-214">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="de453-215">对于组件代码中的每个调用，单个调用可以指定超时：</span><span class="sxs-lookup"><span data-stu-id="de453-215">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="de453-216">有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="de453-216">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="de453-217">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="de453-217">Avoid circular object references</span></span>

<span data-ttu-id="de453-218">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="de453-218">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="de453-219">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="de453-219">.NET method calls.</span></span>
* <span data-ttu-id="de453-220">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="de453-220">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="de453-221">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="de453-221">For more information, see the following issues:</span></span>

* [<span data-ttu-id="de453-222">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="de453-222">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="de453-223">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="de453-223">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="de453-224">其他资源</span><span class="sxs-lookup"><span data-stu-id="de453-224">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="de453-225">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="de453-225">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="de453-226">[在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="de453-226">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
