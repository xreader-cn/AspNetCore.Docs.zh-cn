---
title: Razor JavaScript 组件互操作
author: guardrex
description: 了解如何调用 JavaScript 函数，从.NET 和.NET 中 JavaScript Blazor 和 Razor 组件应用程序中的方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/javascript-interop
ms.openlocfilehash: f2588f4ed1ec2f01218283625fae4632d0a8ae58
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515429"
---
# <a name="razor-components-javascript-interop"></a><span data-ttu-id="7e323-103">Razor JavaScript 组件互操作</span><span class="sxs-lookup"><span data-stu-id="7e323-103">Razor Components JavaScript interop</span></span>

<span data-ttu-id="7e323-104">通过[Javier Calvarro Nelson](https://github.com/javiercn)， [Daniel Roth](https://github.com/danroth27)，和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7e323-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7e323-105">Razor 组件应用程序可以调用 JavaScript 函数，从.NET 和.NET 中的 JavaScript 代码的方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-105">A Razor Components app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="7e323-106">调用 JavaScript 函数的.NET 方法</span><span class="sxs-lookup"><span data-stu-id="7e323-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="7e323-107">有些的时候需要调用 JavaScript 函数的.NET 代码时。</span><span class="sxs-lookup"><span data-stu-id="7e323-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="7e323-108">例如，浏览器功能或从 JavaScript 库向应用程序的功能，可以公开 JavaScript 调用。</span><span class="sxs-lookup"><span data-stu-id="7e323-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="7e323-109">若要从.NET 调用的 JavaScript，请使用`IJSRuntime`抽象。</span><span class="sxs-lookup"><span data-stu-id="7e323-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="7e323-110">`InvokeAsync<T>`方法采用要以及任意数量的 JSON 可序列化参数调用的 JavaScript 函数的标识符。</span><span class="sxs-lookup"><span data-stu-id="7e323-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="7e323-111">函数标识符是相对于全局作用域 (`window`)。</span><span class="sxs-lookup"><span data-stu-id="7e323-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="7e323-112">如果你想要调用`window.someScope.someFunction`，该标识符是`someScope.someFunction`。</span><span class="sxs-lookup"><span data-stu-id="7e323-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="7e323-113">没有无需注册该函数之前调用它。</span><span class="sxs-lookup"><span data-stu-id="7e323-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="7e323-114">返回类型`T`还必须是 JSON 可序列化。</span><span class="sxs-lookup"><span data-stu-id="7e323-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="7e323-115">对于服务器端 ASP.NET Core Razor 组件应用程序：</span><span class="sxs-lookup"><span data-stu-id="7e323-115">For server-side ASP.NET Core Razor Components apps:</span></span>

* <span data-ttu-id="7e323-116">由服务器端应用程序处理多个用户请求。</span><span class="sxs-lookup"><span data-stu-id="7e323-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="7e323-117">不要调用`JSRuntime.Current`组件调用 JavaScript 函数中。</span><span class="sxs-lookup"><span data-stu-id="7e323-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="7e323-118">注入`IJSRuntime`抽象和使用注入的对象发出 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="7e323-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>

<span data-ttu-id="7e323-119">下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)，实验性的基于 JavaScript 的解码器。</span><span class="sxs-lookup"><span data-stu-id="7e323-119">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="7e323-120">该示例演示如何调用一个 JavaScript 函数中的C#方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-120">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="7e323-121">JavaScript 函数接受字节数组从C#方法，对数组进行解码并返回到显示的组件的文本。</span><span class="sxs-lookup"><span data-stu-id="7e323-121">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="7e323-122">内部`<head>`的元素*wwwroot/index.html*，提供一个函数来使用`TextDecoder`传递的数组进行解码：</span><span class="sxs-lookup"><span data-stu-id="7e323-122">Inside the `<head>` element of *wwwroot/index.html*, provide a function that uses `TextDecoder` to decode a passed array:</span></span>

```html
<script>
  window.ConvertArray = (win1251Array) => {
    var win1251decoder = new TextDecoder('windows-1251');
    var bytes = new Uint8Array(win1251Array);
    var decodedArray = win1251decoder.decode(bytes);
    console.log(decodedArray);
    return decodedArray;
  };
</script>
```

<span data-ttu-id="7e323-123">此外可以从一个 JavaScript 文件加载 JavaScript 代码，例如在前面的示例所示的代码 (*.js*) 中的脚本文件引用*wwwroot/index.html*文件：</span><span class="sxs-lookup"><span data-stu-id="7e323-123">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file in the *wwwroot/index.html* file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="7e323-124">以下组件：</span><span class="sxs-lookup"><span data-stu-id="7e323-124">The following component:</span></span>

* <span data-ttu-id="7e323-125">调用`ConvertArray`JavaScript 函数使用`JsRuntime`的组件按钮时 (**转换数组**) 处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="7e323-125">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="7e323-126">JavaScript 函数调用之后，则将传递的数组转换为字符串。</span><span class="sxs-lookup"><span data-stu-id="7e323-126">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="7e323-127">供显示的组件返回的字符串。</span><span class="sxs-lookup"><span data-stu-id="7e323-127">The string is returned to the component for display.</span></span>

```cshtml
@page "/"
@inject IJSRuntime JsRuntime;

<h1>Call JavaScript Function Example</h1>

<button type="button" class="btn btn-primary" onclick="@ConvertArray">
    Convert Array
</button>

<p class="mt-2" style="font-size:1.6em">
    <span class="badge badge-success">
        @ConvertedText
    </span>
</p>

@functions {
    // Quote (c)2005 Universal Pictures: Serenity
    // https://www.uphe.com/movies/serenity
    // David Krumholtz on IMDB: https://www.imdb.com/name/nm0472710/

    private MarkupString ConvertedText =
        new MarkupString("Select the <b>Convert Array</b> button.");

    private uint[] QuoteArray = new uint[]
        {
            60, 101, 109, 62, 67, 97, 110, 39, 116, 32, 115, 116, 111, 112, 32,
            116, 104, 101, 32, 115, 105, 103, 110, 97, 108, 44, 32, 77, 97,
            108, 46, 60, 47, 101, 109, 62, 32, 45, 32, 77, 114, 46, 32, 85, 110,
            105, 118, 101, 114, 115, 101, 10, 10,
        };

    private async void ConvertArray()
    {
        var text =
            await JsRuntime.InvokeAsync<string>("ConvertArray", QuoteArray);

        ConvertedText = new MarkupString(text);

        StateHasChanged();
    }
}
```

<span data-ttu-id="7e323-128">若要使用`IJSRuntime`抽象，采用任何以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="7e323-128">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="7e323-129">注入`IJSRuntime`抽象到 Razor 文件 (*.razor*， *.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="7e323-129">Inject the `IJSRuntime` abstraction into the Razor file (*.razor*, *.cshtml*):</span></span>

  ```cshtml
  @inject IJSRuntime JSRuntime

  @functions {
      public override void OnInit()
      {
          StocksService.OnStockTickerUpdated += stockUpdate =>
          {
              JSRuntime.InvokeAsync<object>(
                  "handleTickerChanged",
                  stockUpdate.symbol,
                  stockUpdate.price);
          };
      }
  }
  ```

* <span data-ttu-id="7e323-130">注入`IJSRuntime`抽象到一个类 (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="7e323-130">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  ```csharp
  public class JsInteropClasses
  {
      private readonly IJSRuntime _jsRuntime;

      public JsInteropClasses(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public Task<string> TickerChanged(string data)
      {
          // The handleTickerChanged JavaScript method is implemented
          // in a JavaScript file, such as 'wwwroot/tickerJsInterop.js'.
          return _jsRuntime.InvokeAsync<object>(
              "handleTickerChanged",
              stockUpdate.symbol,
              stockUpdate.price);
      }
  }
  ```

* <span data-ttu-id="7e323-131">有关使用动态内容生成`BuildRenderTree`，使用`[Inject]`属性：</span><span class="sxs-lookup"><span data-stu-id="7e323-131">For dynamic content generation with `BuildRenderTree`, use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="7e323-132">在客户端的示例应用中本主题附带，两个 JavaScript 函数可供与 DOM 来接收用户输入并显示一条欢迎消息进行交互的客户端应用：</span><span class="sxs-lookup"><span data-stu-id="7e323-132">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* `showPrompt` <span data-ttu-id="7e323-133">&ndash; 生成提示接受用户输入 （用户的名称），并返回给调用方的名称。</span><span class="sxs-lookup"><span data-stu-id="7e323-133">&ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* `displayWelcome` <span data-ttu-id="7e323-134">&ndash; 将一条欢迎消息从调用方分配给具有的 DOM 对象`id`的`welcome`。</span><span class="sxs-lookup"><span data-stu-id="7e323-134">&ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="7e323-135">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="7e323-135">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="7e323-136">位置`<script>`引用的 JavaScript 文件中的标记*wwwroot/index.html*文件：</span><span class="sxs-lookup"><span data-stu-id="7e323-136">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file:</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="7e323-137">不放置`<script>`标记中的组件文件，因为`<script>`标记不能进行动态更新。</span><span class="sxs-lookup"><span data-stu-id="7e323-137">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="7e323-138">使用 JavaScript 的.NET 方法互操作中的函数*exampleJsInterop.js*通过调用文件`IJSRuntime.InvokeAsync<T>`。</span><span class="sxs-lookup"><span data-stu-id="7e323-138">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="7e323-139">`IJSRuntime`抽象是异步模式，以允许服务器端的方案。</span><span class="sxs-lookup"><span data-stu-id="7e323-139">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="7e323-140">如果应用在运行客户端，并且你想要调用的 JavaScript 函数以同步方式，向下转换到`IJSInProcessRuntime`，并调用`Invoke<T>`相反。</span><span class="sxs-lookup"><span data-stu-id="7e323-140">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="7e323-141">我们建议互操作的大多数 JavaScript 库，使用异步 Api，以确保的库都可在所有情况下，客户端或服务器端。</span><span class="sxs-lookup"><span data-stu-id="7e323-141">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="7e323-142">示例应用包含用于演示 JavaScript 互操作的组件。</span><span class="sxs-lookup"><span data-stu-id="7e323-142">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="7e323-143">组件：</span><span class="sxs-lookup"><span data-stu-id="7e323-143">The component:</span></span>

* <span data-ttu-id="7e323-144">接收通过 JavaScript 提示用户输入。</span><span class="sxs-lookup"><span data-stu-id="7e323-144">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="7e323-145">返回对组件进行处理的文本。</span><span class="sxs-lookup"><span data-stu-id="7e323-145">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="7e323-146">调用与 DOM 以显示一条欢迎消息进行交互的第二个 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="7e323-146">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="7e323-147">*Pages/JSInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7e323-147">*Pages/JSInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="7e323-148">当`TriggerJsPrompt`选择该组件的执行**触发器 JavaScript 提示**按钮，JavaScript`showPrompt`函数中提供*wwwroot/exampleJsInterop.js*文件调用。</span><span class="sxs-lookup"><span data-stu-id="7e323-148">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="7e323-149">`showPrompt`函数接受用户输入 （用户的名称），这是 HTML 编码并返回到该组件。</span><span class="sxs-lookup"><span data-stu-id="7e323-149">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="7e323-150">该组件将用户的名称存储在本地变量中， `name`。</span><span class="sxs-lookup"><span data-stu-id="7e323-150">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="7e323-151">将字符串存储在`name`合并到一条欢迎消息，这传递给 JavaScript 函数， `displayWelcome`，它的标题标记将呈现的欢迎消息。</span><span class="sxs-lookup"><span data-stu-id="7e323-151">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="capture-references-to-elements"></a><span data-ttu-id="7e323-152">捕获对元素的引用</span><span class="sxs-lookup"><span data-stu-id="7e323-152">Capture references to elements</span></span>

<span data-ttu-id="7e323-153">某些[JavaScript 互操作](xref:razor-components/javascript-interop)情况下，需要对 HTML 元素的引用。</span><span class="sxs-lookup"><span data-stu-id="7e323-153">Some [JavaScript interop](xref:razor-components/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="7e323-154">例如，用户界面库可能需要元素引用进行初始化，或者可能需要在元素上，调用类似于命令的 Api，例如`focus`或`play`。</span><span class="sxs-lookup"><span data-stu-id="7e323-154">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="7e323-155">你可以通过添加捕获对组件中的 HTML 元素的引用`ref`属性的 HTML 元素，然后定义类型的字段`ElementRef`其名称与匹配的值`ref`属性。</span><span class="sxs-lookup"><span data-stu-id="7e323-155">You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="7e323-156">下面的示例显示了捕获用户名输入元素的引用：</span><span class="sxs-lookup"><span data-stu-id="7e323-156">The following example shows capturing a reference to the username input element:</span></span>

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="7e323-157">不要**不**作为填充 DOM 的一种方法使用捕获的元素引用</span><span class="sxs-lookup"><span data-stu-id="7e323-157">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="7e323-158">执行此操作可能会影响声明性呈现模型。</span><span class="sxs-lookup"><span data-stu-id="7e323-158">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="7e323-159">就.NET 代码而言，`ElementRef`是不透明的句柄。</span><span class="sxs-lookup"><span data-stu-id="7e323-159">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="7e323-160">*仅*可以用做的事情`ElementRef`是通过 JavaScript 互操作将通过其传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="7e323-160">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="7e323-161">当你执行此操作时，在 JavaScript 端代码接收`HTMLElement`实例，它可以与普通的 DOM Api 配合使用。</span><span class="sxs-lookup"><span data-stu-id="7e323-161">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="7e323-162">例如，下面的代码定义一个.NET 扩展方法，使将焦点设置在元素上：</span><span class="sxs-lookup"><span data-stu-id="7e323-162">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="7e323-163">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="7e323-163">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="7e323-164">使用`IJSRuntime.InvokeAsync<T>`，并调用`exampleJsFunctions.focusElement`与`ElementRef`能够将精力集中元素：</span><span class="sxs-lookup"><span data-stu-id="7e323-164">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.cshtml?highlight=1,3,7,11-12)]

<span data-ttu-id="7e323-165">若要使用的扩展方法可以将精力集中元素，创建一个静态扩展方法可接收`IJSRuntime`实例：</span><span class="sxs-lookup"><span data-stu-id="7e323-165">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="7e323-166">直接在对象上调用方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-166">The method is called directly on the object.</span></span> <span data-ttu-id="7e323-167">下面的示例假定静态`Focus`方法是可从`JsInteropClasses`命名空间：</span><span class="sxs-lookup"><span data-stu-id="7e323-167">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.cshtml?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="7e323-168">`username`组件呈现并包括其输出后，仅填充变量`>`元素。</span><span class="sxs-lookup"><span data-stu-id="7e323-168">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="7e323-169">如果你尝试传递即便`ElementRef`向 JavaScript 代码的 JavaScript 代码接收`null`。</span><span class="sxs-lookup"><span data-stu-id="7e323-169">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="7e323-170">操作元素的引用，该组件已经完成呈现 （将在元素上设置初始焦点） 使用后`OnAfterRenderAsync`或`OnAfterRender`[组件生命周期方法](xref:razor-components/components#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="7e323-170">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:razor-components/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="7e323-171">调用 JavaScript 函数中的.NET 方法</span><span class="sxs-lookup"><span data-stu-id="7e323-171">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="7e323-172">静态.NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="7e323-172">Static .NET method call</span></span>

<span data-ttu-id="7e323-173">若要调用静态.NET 方法从 JavaScript，请使用`DotNet.invokeMethod`或`DotNet.invokeMethodAsync`函数。</span><span class="sxs-lookup"><span data-stu-id="7e323-173">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="7e323-174">传入你想要调用的函数，并且任何自变量包含的程序集名称的静态方法的标识符。</span><span class="sxs-lookup"><span data-stu-id="7e323-174">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="7e323-175">首选的异步版本来支持服务器端的方案。</span><span class="sxs-lookup"><span data-stu-id="7e323-175">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="7e323-176">若要成为可从 JavaScript 调用，.NET 方法必须是公共、 静态的并使用经过修饰`[JSInvokable]`。</span><span class="sxs-lookup"><span data-stu-id="7e323-176">To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`.</span></span> <span data-ttu-id="7e323-177">默认情况下，方法标识符是方法名称，但可以指定不同的标识符使用`JSInvokableAttribute`构造函数。</span><span class="sxs-lookup"><span data-stu-id="7e323-177">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="7e323-178">调用开放式泛型方法当前不支持。</span><span class="sxs-lookup"><span data-stu-id="7e323-178">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="7e323-179">示例应用包含C#方法返回的数组`int`s。</span><span class="sxs-lookup"><span data-stu-id="7e323-179">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="7e323-180">该方法用修饰`JSInvokable`属性。</span><span class="sxs-lookup"><span data-stu-id="7e323-180">The method is decorated with the `JSInvokable` attribute.</span></span>

<span data-ttu-id="7e323-181">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7e323-181">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="7e323-182">客户端的 JavaScript 调用C#.NET 方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-182">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="7e323-183">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="7e323-183">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="7e323-184">当**触发器.NET 静态方法 ReturnArrayAsync**按钮处于选中状态，请查看浏览器的 web 开发人员工具中的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="7e323-184">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="7e323-185">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="7e323-185">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="7e323-186">第四个数组值推送到的数组 (`data.push(4);`) 返回的`ReturnArrayAsync`。</span><span class="sxs-lookup"><span data-stu-id="7e323-186">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="7e323-187">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="7e323-187">Instance method call</span></span>

<span data-ttu-id="7e323-188">此外可以从 JavaScript 调用.NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-188">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="7e323-189">若要调用的 JavaScript 的.NET 实例方法，第一次的实例传递给.NET 到 JavaScript 通过包装在`DotNetObjectRef`实例。</span><span class="sxs-lookup"><span data-stu-id="7e323-189">To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="7e323-190">.NET 实例通过引用传递给 JavaScript，并可调用实例使用的.NET 实例方法`invokeMethod`或`invokeMethodAsync`函数。</span><span class="sxs-lookup"><span data-stu-id="7e323-190">The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="7e323-191">调用其他.NET 方法从 JavaScript 时，还可以作为参数传递.NET 实例。</span><span class="sxs-lookup"><span data-stu-id="7e323-191">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="7e323-192">示例应用程序将消息记录到客户端的控制台。</span><span class="sxs-lookup"><span data-stu-id="7e323-192">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="7e323-193">对于以下示例所演示的示例应用中，检查浏览器的开发人员工具中的浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="7e323-193">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="7e323-194">当**触发器.NET 实例方法 HelloHelper.SayHello**选择按钮时，`ExampleJsInterop.CallHelloHelperSayHello`调用，并将传递一个名称， `Blazor`，给该方法。</span><span class="sxs-lookup"><span data-stu-id="7e323-194">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="7e323-195">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7e323-195">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` <span data-ttu-id="7e323-196">调用 JavaScript 函数`sayHello`新实例的`HelloHelper`。</span><span class="sxs-lookup"><span data-stu-id="7e323-196">invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="7e323-197">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e323-197">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="7e323-198">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="7e323-198">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="7e323-199">名称传递给`HelloHelper`的构造函数中，这会设置`HelloHelper.Name`属性。</span><span class="sxs-lookup"><span data-stu-id="7e323-199">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="7e323-200">当 JavaScript 函数`sayHello`执行时，`HelloHelper.SayHello`返回`Hello, {Name}!`消息，通过 JavaScript 函数写入到控制台。</span><span class="sxs-lookup"><span data-stu-id="7e323-200">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="7e323-201">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e323-201">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="7e323-202">控制台输出中浏览器的 web 开发人员工具：</span><span class="sxs-lookup"><span data-stu-id="7e323-202">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a><span data-ttu-id="7e323-203">共享组件 Razor 类库中的互操作代码</span><span class="sxs-lookup"><span data-stu-id="7e323-203">Share interop code in a Razor Component class library</span></span>

<span data-ttu-id="7e323-204">可以在 Razor 组件的类库中包含 JavaScript 互操作代码 (`dotnet new razorclasslib`)，这样您就可以共享 NuGet 程序包中的代码。</span><span class="sxs-lookup"><span data-stu-id="7e323-204">JavaScript interop code can be included in a Razor Component class library (`dotnet new razorclasslib`), which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="7e323-205">Razor 组件类库处理生成的程序集中嵌入的 JavaScript 资源。</span><span class="sxs-lookup"><span data-stu-id="7e323-205">The Razor Component class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="7e323-206">JavaScript 文件将放入*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7e323-206">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="7e323-207">工具将负责生成库时，嵌入资源。</span><span class="sxs-lookup"><span data-stu-id="7e323-207">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="7e323-208">就像引用任何普通的 NuGet 包，生成的 NuGet 程序包引用在项目文件中的应用程序。</span><span class="sxs-lookup"><span data-stu-id="7e323-208">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="7e323-209">就像还原应用程序后，应用程序代码可以调用到 JavaScript C#。</span><span class="sxs-lookup"><span data-stu-id="7e323-209">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="7e323-210">有关详细信息，请参阅 <xref:razor-components/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="7e323-210">For more information, see <xref:razor-components/class-libraries>.</span></span>
