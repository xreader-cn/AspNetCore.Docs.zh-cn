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
ms.openlocfilehash: 53b702cddca778e06e617df3798bffb21677d36b
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751642"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数

作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Pranav Krishnamoorthy](https://github.com/pranavkm) 和 [Luke Latham](https://github.com/guardrex)

Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。 这被称为 JavaScript 互操作（JS 互操作） 。

本文介绍如何从 .NET 调用 JavaScript 函数。 有关如何从 JavaScript 调用 .NET 方法的信息，请参阅 <xref:blazor/call-dotnet-from-javascript>。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

> [!NOTE]
> 将 JS 文件（`<script>` 标记）添加到 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中的 `</body>` 结束标记前。 确保在 Blazor framework JS 文件之前包含带有 JS 互操作方法的 JS 文件。

若要从 .NET 调入 JavaScript，请使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。 若要发出 JS 互操作调用，请在组件中注入 <xref:Microsoft.JSInterop.IJSRuntime> 抽象。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 需要使用你要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。 函数标识符相对于全局范围 (`window`)。 如果要调用 `window.someScope.someFunction`，则标识符是 `someScope.someFunction`。 无需在调用函数之前进行注册。 返回类型 `T` 也必须可进行 JSON 序列化。 `T` 应该与最能映射到所返回 JSON 类型的 .NET 类型匹配。

返回 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 JavaScript 函数使用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 调用。 `InvokeAsync` 会将 Promise 解包并返回 Promise 所等待的值。

对于启用了预呈现的 Blazor Server 应用，初始预呈现期间无法调入 JavaScript。 在建立与浏览器的连接之后，必须延迟 JavaScript 互操作调用。 有关详细信息，请参阅[检测 Blazor Server 应用进行预呈现的时间](#detect-when-a-blazor-server-app-is-prerendering)部分。

下面的示例基于 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)（一种基于 JavaScript 的解码器）。 此示例展示了如何通过 C# 方法调用 JavaScript 函数，以将要求从开发人员代码卸载到现有 JavaScript API。 JavaScript 函数从 C# 方法接受字节数组，对数组进行解码，并将文本返回给组件进行显示。

在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供了一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript 代码（如前面示例中所示的代码）也可以通过对脚本文件的引用，从 JavaScript 文件 (`.js`) 加载：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 在选择了组件按钮（“`Convert Array`”）后，使用 `JS` 调用 `convertArray` JavaScript 函数。
* 调用 JavaScript 函数之后，传递的数组会转换为字符串。 该字符串会返回给组件进行显示。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象，请采用以下任何方法：

* 将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入 Razor 组件 (`.razor`) 中：

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。 该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* 将 <xref:Microsoft.JSInterop.IJSRuntime> 抽象注入类 (`.cs`)：

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。 该函数通过 `JS.InvokeAsync` 进行调用，会返回值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* 对于使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) 的动态内容生成，请使用 `[Inject]` 属性：

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

在本主题附带的客户端示例应用中，向应用提供了两个 JavaScript 函数，可与 DOM 交互以接收用户输入并显示欢迎消息：

* `showPrompt`：生成一个提示，用于接受用户输入（用户名），并将用户名返回给调用方。
* `displayWelcome`：将来自调用方的欢迎消息分配给 `id` 为 `welcome` 的 DOM 对象。

`wwwroot/exampleJsInterop.js`：

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

将引用 JavaScript 文件的 `<script>` 标记置于 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中。

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

请勿将 `<script>` 标记置于组件文件中，因为 `<script>` 标记无法动态更新。

.NET 方法通过调用 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 与 `exampleJsInterop.js` 文件中的 JavaScript 函数进行互操作。

<xref:Microsoft.JSInterop.IJSRuntime> 抽象是异步的，以便可以实现 Blazor Server 方案。 如果应用是 Blazor WebAssembly 应用，并且要同步调用 JavaScript 函数，则向下转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 并改为调用 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>。 建议大多数 JS 互操作库使用异步 API，以确保库在所有方案中都可用。

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 若要在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离，请参阅 [BlazorJavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)部分。

::: moniker-end

该示例应用包含一个用于演示 JS 互操作的组件。 该组件：

* 通过 JavaScript 提示接收用户输入。
* 将文本返回给组件进行处理。
* 调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。

`Pages/JsInterop.razor`:

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

占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。

1. 通过选择组件的“`Trigger JavaScript Prompt`”按钮来执行 `TriggerJsPrompt` 时，则会调用在 `wwwroot/exampleJsInterop.js` 文件中提供的 JavaScript `showPrompt` 函数。
1. `showPrompt` 函数接受进行 HTML 编码并返回给组件的用户输入（用户的名称）。 组件将用户的名称存储在本地变量 `name` 中。
1. 存储在 `name` 中的字符串会合并为欢迎消息，而该消息会传递给 JavaScript 函数 `displayWelcome`（它将欢迎消息呈现到标题标记中）。

## <a name="call-a-void-javascript-function"></a>调用 void JavaScript 函数

在以下场景中使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>：

* 返回 [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函数。
* 如果 .NET 不需要读取 JavaScript 调用的结果。

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>检测 Blazor Server 应用进行预呈现的时间
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些 JS 互操作方案需要引用 HTML 元素。 例如，一个 UI 库可能需要用于初始化的元素引用，或者你可能需要对元素调用类似于命令的 API（如 `focus` 或 `play`）。

使用以下方法在组件中捕获对 HTML 元素的引用：

* 向 HTML 元素添加 `@ref` 属性。
* 定义一个类型为 <xref:Microsoft.AspNetCore.Components.ElementReference> 字段，其名称与 `@ref` 属性的值匹配。

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
> 在下面的示例中，改变无序列表 (`ul`) 的内容具有危险性，因为 Blazor 会与 DOM 交互以填充此元素的列表项 (`<li>`)：
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

通过 JS 互操作将 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码。 JavaScript 代码会收到一个 `HTMLElement` 实例，该实例可以与常规 DOM API 一起使用。 例如，下面的代码定义了一个 .NET 扩展方法，该方法的作用是将鼠标单击事件发送到某个元素：

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 在 C# 代码中使用 [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) 来聚焦元素，该元素内置于 Blazor 框架，并与元素引用结合使用。

::: moniker-end

若要调用不返回值的 JavaScript 函数，请使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>。 下面的代码使用捕获的 <xref:Microsoft.AspNetCore.Components.ElementReference> 调用上面的 JavaScript 函数，触发客户端 `Click` 事件：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

若要使用扩展方法，请创建接收 <xref:Microsoft.JSInterop.IJSRuntime> 实例的静态扩展方法：

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

`clickElement` 方法在对象上直接调用。 下面的示例假设可从 `JsInteropClasses` 命名空间使用 `TriggerClickEvent` 方法：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> 仅在呈现组件后填充 `exampleButton` 变量。 如果将未填充的 <xref:Microsoft.AspNetCore.Components.ElementReference> 传递给 JavaScript 代码，则 JavaScript 代码会收到 `null` 值。 若要在组件完成呈现后操作元素引用，请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 组件生命周期方法](xref:blazor/components/lifecycle#after-component-render)。

使用泛型类型并返回值时，请使用 <xref:System.Threading.Tasks.ValueTask%601>：

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` 在具有类型的对象上直接调用。 下面的示例假设可从 `JsInteropClasses` 命名空间使用 `GenericMethod`：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>跨组件引用元素

不能在组件之间传递 <xref:Microsoft.AspNetCore.Components.ElementReference>，因为：

* 仅在呈现组件之后（即在执行组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 方法期间或之后），才能保证实例存在。
* <xref:Microsoft.AspNetCore.Components.ElementReference> 是 [`struct`](/csharp/language-reference/builtin-types/struct)，不能作为[组件参数](xref:blazor/components/index#component-parameters)传递。

若要使父组件可以向其他组件提供元素引用，父组件可以：

* 允许子组件注册回调。
* 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 事件期间，通过传递的元素引用调用注册的回调。 此方法间接地允许子组件与父级的元素引用交互。

下面的 Blazor WebAssembly 示例阐释了这种方法。

在 `wwwroot/index.html` 的 `<head>` 中：

```html
<style>
    .red { color: red }
</style>
```

在 `wwwroot/index.html` 的 `<body>` 中：

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor`（父组件）：

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

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

占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。

`Shared/SurveyPrompt.razor`（子组件）：

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

`Shared/SurveyPrompt.razor.cs`:

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

占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。

## <a name="harden-js-interop-calls"></a>强化 JS 互操作调用

JS 互操作可能会由于网络错误而失败，因此应视为不可靠。 默认情况下，Blazor Server 应用会在一分钟后，使服务器上的 JS 互操作调用超时。 如果应用可以容忍更激进的超时，请使用以下方法之一设置超时：

* 在 `Startup.ConfigureServices` 中全局指定超时：

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 对于组件代码中的每个调用，单个调用可以指定超时：

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

有关资源耗尽的详细信息，请参阅 <xref:blazor/security/server/threat-mitigation>。

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>避免循环引用对象

不能在客户端上针对以下调用就包含循环引用的对象进行序列化：

* .NET 方法调用。
* 返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。

有关详细信息，请参阅[循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)。

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 隔离和对象引用

Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。 JavaScript 隔离具有以下优势：

* 导入的 JavaScript 不再污染全局命名空间。
* 库和组件的使用者不需要导入相关的 JavaScript。

例如，以下 JavaScript 模块导出用于显示浏览器提示的 JavaScript 函数：

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

将前面的 JavaScript 模块作为静态 Web 资产 (`wwwroot/exampleJsInterop.js`) 添加到 .NET 库，然后使用 <xref:Microsoft.JSInterop.IJSRuntime> 服务将该模块导入 .NET 代码。 以下示例将服务作为 `js`（未显示）注入：

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

上例中的 `import` 标识符是专门用于导入 JavaScript 模块的特殊标识符。 使用模块的稳定静态 Web 资产路径 `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 指定模块。 若要创建 JavaScript 文件的正确静态资产路径，需要当前目录 (`./`) 的路径段。 占位符 `{LIBRARY NAME}` 是库的名称。 占位符 `{PATH UNDER WWWROOT}` 是 `wwwroot` 下脚本的路径。

<xref:Microsoft.JSInterop.IJSRuntime> 将模块作为 `IJSObjectReference` 导入，它表示对 .NET 代码中 JavaScript 对象的引用。 使用 `IJSObjectReference` 调用从模块导出的 JavaScript 函数：

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` 表示对某个 JavaScript 对象的引用，该对象的函数可以同步进行调用。

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>使用呈现 UI 的 JavaScript 库（DOM 元素）

有时你可能需要使用在浏览器 DOM 内生成可见用户界面元素的 JavaScript 库。 乍一想，这似乎很难，因为 Blazor 的 diffing 系统依赖于对 DOM 元素树的控制，并且如果某个外部代码使 DOM 树发生变化并为了应用 diff 而使其机制失效，就会产生错误。 这并不是一个特定于 Blazor 的限制。 任何基于 diff 的 UI 框架都会面临同样的问题。

幸运的是，将外部生成的 UI 可靠地嵌入到 Blazor 组件 UI 非常简单。 推荐的方法是让组件的代码（`.razor` 文件）生成一个空元素。 就 Blazor 的 diffing 系统而言，该元素始终为空，这样呈现器就不会递归到元素中，而是保留元素内容不变。 这样就可以安全地用外部托管的任意内容来填充元素。

以下示例演示了这一概念。 在 `if` 语句中，当 `firstRender` 为 `true` 时，使用 `myElement` 来执行某些操作。 例如，调用某个外部 JavaScript 库来填充它。 Blazor 保留元素内容不变，直到此组件本身被删除。 删除组件时，会同时删除组件的整个 DOM 子树。

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

为了更详细地说明，请思考以下组件，该组件使用[开源 Mapbox API](https://www.mapbox.com/) 呈现交互式地图：

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

相应的 JavaScript 模块（应置于 `wwwroot/mapComponent.js` 处）如下所示：

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

在前面的示例中，将字符串 `{ACCESS TOKEN}` 替换为可从 https://account.mapbox.com 中获取的有效访问令牌。

为生成正确的样式，将以下样式表标记添加到主机 HTML 页面中（`index.html` 或 `_Host.cshtml`）：

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

前面的示例会生成一个交互式地图 UI，其中用户：

* 拖动鼠标可以滚动或缩放。
* 单击相关按钮可跳转到预定义的位置。

![Mapbox 的日本东京街道地图，其中设有可用于选择英国布里斯托尔和日本东京的按钮](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

要了解的要点如下：

 * 就 Blazor 而言，具有 `@ref="mapElement"` 的 `<div>` 保留为空。 这样随着时间推移，`mapbox-gl.js` 填充和修改其内容是安全的。 可以将此方法与呈现 UI 的任何 JavaScript 库配合使用。 甚至可以在 Blazor 组件中嵌入第三方 JavaScript SPA 框架中的组件，只要它们不会尝试访问和改变页面的其他部分。 对于外部 JavaScript 代码，修改 Blazor 不视为空元素的元素是 *不* 安全的。
 * 使用此方法时，请记得有关 Blazor 保留或销毁 DOM 元素的方式的规则。 在前面的示例中，组件之所以能够安全处理按钮单击事件并更新现有的地图实例，是因为默认在可能的情况下保留 DOM 元素。 如果之前要从 `@foreach` 循环内呈现地图元素的列表，则需要使用 `@key` 来确保保留组件实例。 否则，列表数据的更改可能导致组件实例以不合适的方式保留以前实例的状态。 有关详细信息，请参阅[使用 @key 保留元素和组件](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)。

此外，上面的示例演示了如何在 ES6 模块中封装 JavaScript 逻辑和依赖关系，并使用 `import` 标识符动态加载该模块。 有关详细信息，请参阅 [JavaScript 隔离和对象引用](#blazor-javascript-isolation-and-object-references)。

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>对 JS 互操作调用的大小限制

在 Blazor WebAssembly 中，框架对 JS 互操作输入和输出的大小不施加限制。

在 Blazor Server 中，JS 互操作调用的大小受中心方法允许的最大传入 SignalR 消息大小限制，该限制由 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>（默认值：32 KB）实施。 JS 到 .NET 的 SignalR 消息大于 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 时会引发错误。 框架对从中心到客户端的 SignalR 消息大小不施加限制。 有关详细信息，请参阅 <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>。
  
## <a name="js-modules"></a>JS 模块

对于 JS 隔离，JS 互操作适用于浏览器针对 [EcmaScript 模块 (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)) 的默认支持。

## <a name="unmarshalled-js-interop"></a>未封装的 JS 互操作

当针对 JS 互操作序列化 .NET 对象并且满足以下任一条件时，Blazor WebAssembly 组件的性能可能会较差：

* 大量 .NET 对象迅速地进行序列化。 示例：通过移动输入设备（如旋转鼠标滚轮）来进行 JS 互操作调用。
* 对于 JS 互操作，必须序列化大型 .NET 对象或多个 .NET 对象。 示例：JS 互操作调用需要序列化数十个文件。

<xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> 表示对某个 JavaScript 对象的引用，该对象的函数无需 .NET 数据序列化开销即可调用。

在以下示例中：

* 包含字符串和整数的 [struct](/dotnet/csharp/language-reference/builtin-types/struct) 会以非序列化方式传递给 JavaScript。
* JavaScript 函数处理数据，并将布尔或字符串返回给调用方。
* JavaScript 字符串无法直接转换为 .NET `string` 对象。 `unmarshalledFunctionReturnString` 函数调用 `BINDING.js_string_to_mono_string` 来管理 Javascript 字符串的转换。

> [!NOTE]
> 以下示例不是此方案的典型用例，因为传递给 JavaScript 的 [struct](/dotnet/csharp/language-reference/builtin-types/struct) 不会导致组件性能变差。 该示例仅使用一个小型对象来演示传递未序列化 .NET 数据的概念。

`wwwroot/index.html` 中 `<script>` 块的内容或 `wwwroot/index.html` 引用的外部 Javascript 文件的内容：

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
> `js_string_to_mono_string` 函数的名称、行为和存在可能会在 .NET 的将来版本中更改。 例如：
>
> * 函数可能已重命名。
> * 函数本身可能会被删除，以便能够通过框架自动转换字符串。

`Pages/UnmarshalledJSInterop.razor`（URL：`/unmarshalled-js-interop`）：

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

如果 `IJSUnmarshalledObjectReference` 实例未使用 C# 代码释放，则可以使用 JavaScript 释放。 以下 `dispose` 函数在从 JavaScript 调用时释放对象引用：

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

可以使用 `js_typed_array_to_array` 将数组类型从 JavaScript 对象转换为 .NET 对象，但 JavaScript 数组必须为类型化数组。 可以使用 C# 代码将 JavaScript 中的数组作为 .NET 对象数组 (`object[]`) 进行读取。

可以转换其他数据类型（如字符串数组），但需要创建一个新的 Mono 数组对象 (`mono_obj_array_new`) 并设置其值 (`mono_obj_array_set`)。

> [!WARNING]
> Blazor 框架提供的 JavaScript 函数（如 `js_typed_array_to_array`、`mono_obj_array_new` 和 `mono_obj_array_set`）可能会在 .NET 的未来版本中进行名称更改、行为更改或删除。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
