---
title: 在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数
author: guardrex
description: 了解如何在 Blazor 应用中从 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 7a27b6f1be2ef296d5b2b2a4f566e0cdedbe6480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647520"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数

作者：[Javier Calvarro Nelson](https://github.com/javiercn)[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。 这被称为 JavaScript 互操作（JS 互操作）   。

本文介绍如何从 .NET 调用 JavaScript 函数。 有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。 若要发出 JS 互操作调用，请在组件中注入 `IJSRuntime` 抽象。 `InvokeAsync<T>` 方法采用要与任意数量的 JSON 可序列化参数一起调用的 JavaScript 函数的标识符。 函数标识符相对于全局范围 (`window`)。 如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。 无需在调用函数之前进行注册。 返回类型 `T` 也必须可进行 JSON 序列化。 `T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。

对于启用了预呈现的 Blazor 服务器应用，初始预呈现期间无法调入 JavaScript。 在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。 有关详细信息，请参阅[检测 Blazor 服务器应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。

下面的示例基于 [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。 该示例演示如何从 C# 方法调用 JavaScript 函数。 JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。

在 wwwroot/index.html`<head>` *(* WebAssembly) 或 Pages/_Host.cshtmlBlazor *（* 服务器）的 Blazor 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (.js  ) 加载：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 在选择了组件按钮（“转换数组”`convertArray``JSRuntime`）时使用 **调用** JavaScript 函数。
* 调用 JavaScript 函数之后，传递的数组会转换为字符串。 该字符串会返回给组件进行显示。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

若要使用 `IJSRuntime` 抽象，请采用以下任何方法：

* 将 `IJSRuntime` 抽象注入 Razor 组件 (.razor  ) 中：

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  在 wwwroot/index.html`<head>` *(* WebAssembly) 或 Pages/_Host.cshtmlBlazor *（* 服务器）的 Blazor 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。 该函数通过 `IJSRuntime.InvokeVoidAsync` 进行调用，不返回值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* 将 `IJSRuntime` 抽象注入一个类 (.cs  )：

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  在 wwwroot/index.html`<head>` *(* WebAssembly) 或 Pages/_Host.cshtmlBlazor *（* 服务器）的 Blazor 元素中，提供了一个 `handleTickerChanged` JavaScript 函数。 该函数通过 `JSRuntime.InvokeAsync` 进行调用，会返回值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* 对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：

* `showPrompt` &ndash; 生成一个提示，以接受用户输入（用户名）并将名称返回给调用方。
* `displayWelcome` &ndash; 将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。

wwwroot/exampleJsInterop.js  ：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

将引用 JavaScript 文件的 `<script>` 标记置于 wwwroot/index.html  文件 (Blazor WebAssembly) 或 Pages/_Host.cshtml  文件（Blazor 服务器）中。

wwwroot/index.html  (Blazor WebAssembly)：

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

Pages/_Host.cshtml  （Blazor 服务器）：

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。

.NET 方法通过调用  *与 exampleJsInterop.js*`IJSRuntime.InvokeAsync<T>` 文件中的 JavaScript 函数进行互操作。

`IJSRuntime` 抽象是异步的，以便可以实现 Blazor 服务器方案。 如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。 建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。

该示例应用包含一个用于演示 JS 互操作的组件。 该组件：

* 通过 JavaScript 提示接收用户输入。
* 将文本返回给组件进行处理。
* 调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。

Pages/JSInterop.razor  ：

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

1. 通过选择组件的“触发 JavaScript 提示符”`TriggerJsPrompt`**按钮来执行**  时，则会调用在 wwwroot/exampleJsInterop.js`showPrompt`*文件中提供的 JavaScript* 函数。
1. `showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。 组件将用户的名称存储在本地变量 `name` 中。
1. 存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。

## <a name="call-a-void-javascript-function"></a>调用 void JavaScript 函数

返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数使用 `IJSRuntime.InvokeVoidAsync` 进行调用。

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>检测 Blazor 服务器应用进行预呈现的时间
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些 JS 互操作方案需要引用 HTML 元素。 例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。

使用以下方法在组件中捕获对 HTML 元素的引用：

* 向 HTML 元素添加 `@ref` 属性。
* 定义一个类型为 `ElementReference` 字段，其名称与 `@ref` 属性的值匹配。

以下示例演示如何捕获对 `username` `<input>` 元素的引用：

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> 只使用元素引用改变不与 Blazor 交互的空元素的内容。 当第三方 API 向元素提供内容时，此方案十分有用。 由于 Blazor 不与元素交互，因此在 Blazor 的元素表示形式与 DOM 之间不可能存在冲突。
>
> 在下面的示例中，改变无序列表 ( *) 的内容具有危险性*`ul`，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：
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
> 如果 JS 互操作改变元素 `MyList` 的内容，并且 Blazor 尝试将差异应用于元素，则差异与 DOM 不匹配。

就 .NET 代码而言，`ElementReference` 是不透明的句柄。 可以对  *执行的唯一*`ElementReference`操作是通过 JS 互操作将它传递给 JavaScript 代码。 执行此操作时，JavaScript 端代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。

例如，以下代码定义一个 .NET 扩展方法，通过该方法可在元素上设置焦点：

exampleJsInterop.js  ：

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

若要调用不返回值的 JavaScript 函数，请使用 `IJSRuntime.InvokeVoidAsync`。 下面的代码通过使用捕获的 `ElementReference` 调用前面的 JavaScript 函数，在用户名输入上设置焦点：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

若要使用扩展方法，请创建接收 `IJSRuntime` 实例的静态扩展方法：

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` 方法在对象上直接调用。 下面的示例假设可从 `Focus` 命名空间使用 `JsInteropClasses` 方法：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> 仅在呈现组件后填充 `username` 变量。 如果将未填充的 `ElementReference` 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。 若要在组件完成呈现之后操作元素引用（用于对元素设置初始焦点），请使用 [OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。

使用泛型类型并返回值时，请使用 [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1)：

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` 在具有类型的对象上直接调用。 下面的示例假设可从 `GenericMethod` 命名空间使用 `JsInteropClasses`：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>跨组件引用元素

`ElementReference` 仅保证在组件的 `OnAfterRender` 方法中有效（并且元素引用为 `struct`），因此无法在组件之间传递元素引用。

若要使父组件可以向其他组件提供元素引用，父组件可以：

* 允许子组件注册回调。
* 在 `OnAfterRender` 事件期间，通过传递的元素引用调用注册的回调。 此方法间接地允许子组件与父级的元素引用交互。

以下 Blazor WebAssembly 示例演示了该方法。

在 wwwroot/index.html`<head>`*的* 中：

```html
<style>
    .red { color: red }
</style>
```

在 wwwroot/index.html`<body>`*的* 中：

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

Pages/Index.razor  （父组件）：

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

Pages/Index.razor.cs  ：

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

Shared/SurveyPrompt.razor  （子组件）：

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

Shared/SurveyPrompt.razor.cs  ：

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

## <a name="harden-js-interop-calls"></a>强化 JS 互操作调用

JS 互操作可能会由于网络错误而失败，因此应视为不可靠。 默认情况下，Blazor 服务器应用会在一分钟后，使服务器上的 JS 互操作调用超时。 如果应用可以容忍更严格的超时（如 10秒），请使用以下方法之一设置超时：

* 在 `Startup.ConfigureServices` 中全局指定超时：

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 对于组件代码中的每个调用，单个调用可以指定超时：

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server>。

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>其他资源

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
