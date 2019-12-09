---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor 应用中的 JavaScript 的 .NET 和 .NET 方法中调用 JavaScript 函数。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 05225b86701b7a5d5c84dd43afbef70dd1ece228
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944065"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 互操作

作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="invoke-javascript-functions-from-net-methods"></a>从 .NET 方法调用 JavaScript 函数

有时需要 .NET 代码才能调用 JavaScript 函数。 例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。 此方案称为*JavaScript 互操作性*（*JS 互操作*）。

若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。 `InvokeAsync<T>` 方法采用 JavaScript 函数的标识符，该标识符将与任意数量的 JSON 可序列化参数一起调用。 函数标识符相对于全局范围（`window`）。 如果要调用 `window.someScope.someFunction`，则 `someScope.someFunction`标识符。 无需在调用函数之前注册它。 返回类型 `T` 也必须是 JSON 可序列化的。 `T` 应匹配最适合于返回的 JSON 类型的 .NET 类型。

对于 Blazor 服务器应用：

* Blazor Server 应用处理多个用户请求。 请勿在组件中调用 `JSRuntime.Current` 来调用 JavaScript 函数。
* 注入 `IJSRuntime` 抽象，并使用注入的对象颁发 JS 互操作调用。
* 在预呈现 Blazor 应用程序时，无法调用 JavaScript，因为尚未建立与浏览器的连接。 有关详细信息，请参阅[检测何时预呈现 Blazor 应用](#detect-when-a-blazor-app-is-prerendering)部分。

下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。 该示例演示如何从C#方法调用 JavaScript 函数。 JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。

在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供一个 JavaScript 函数，该函数使用 `TextDecoder` 对传递的数组进行解码并返回解码后的值：

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 选择组件按钮（**转换数组**）时使用 `JSRuntime` 调用 `convertArray` JavaScript 函数。
* 调用 JavaScript 函数后，传递的数组将转换为字符串。 将字符串返回到要显示的组件。

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>使用 IJSRuntime

若要使用 `IJSRuntime` 抽象，请采用以下方法之一：

* 将 `IJSRuntime` 抽象插入 Razor 组件（*razor*）：

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。 使用 `IJSRuntime.InvokeVoidAsync` 调用函数，并且不返回值：

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* 将 `IJSRuntime` 抽象插入类（ *.cs*）：

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor Server）的 `<head>` 元素中，提供 `handleTickerChanged` JavaScript 函数。 使用 `JSRuntime.InvokeAsync` 调用函数并返回值：

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* 对于使用[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)的动态内容生成，请使用 `[Inject]` 特性：

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息：

* `showPrompt` &ndash; 会生成一个提示，以接受用户输入（用户的名称），并将名称返回给调用方。
* `displayWelcome` &ndash; 将来自调用方的欢迎消息分配给具有 `welcome``id` 的 DOM 对象。

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

将引用 JavaScript 文件的 `<script>` 标记放在*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/Blazor _Host*

*wwwroot/index.html* （Blazor WebAssembly）：

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

*Pages/_Host cshtml* （Blazor Server）：

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

请勿在组件文件中放置 `<script>` 标记，因为 `<script>` 标记无法动态更新。

通过调用 `IJSRuntime.InvokeAsync<T>`，.NET 方法与*exampleJsInterop*文件中的 JavaScript 函数互操作。

`IJSRuntime` 抽象是异步的，以允许 Blazor 服务器方案。 如果应用是 Blazor WebAssembly 应用，并且希望同步调用 JavaScript 函数，则向下转换到 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。 建议大多数 JS 互操作库使用异步 Api，以确保库在所有方案中都可用。

该示例应用包含一个用于演示 JS 互操作的组件。 组件：

* 通过 JavaScript 提示接收用户输入。
* 将文本返回到组件进行处理。
* 调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。

*Pages/JSInterop*：

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

1. 通过选择组件的 "**触发器 JavaScript" 提示**按钮执行 `TriggerJsPrompt` 时，将调用*wwwroot/exampleJsInterop*文件中提供的 JavaScript `showPrompt` 函数。
1. `showPrompt` 函数接受 HTML 编码并返回到组件的用户输入（用户的名称）。 组件将用户的名称存储在本地变量 `name`中。
1. 存储在 `name` 中的字符串合并为一个欢迎消息，该消息将被传递给 JavaScript 函数，`displayWelcome`，该消息会将欢迎消息呈现到标题标记中。

## <a name="call-a-void-javascript-function"></a>调用 void JavaScript 函数

返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数通过 `IJSRuntime.InvokeVoidAsync`调用。

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a>检测何时预呈现 Blazor 应用
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些 JS 互操作方案需要引用 HTML 元素。 例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素上调用类似命令的 Api，如 `focus` 或 `play`。

使用以下方法捕获对组件中 HTML 元素的引用：

* 将 `@ref` 特性添加到 HTML 元素中。
* 定义一个类型 `ElementReference` 字段，其名称与 `@ref` 属性的值相匹配。

下面的示例演示捕获对 `username` `<input>` 元素的引用：

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> 只使用元素引用来改变不与 Blazor进行交互的空元素的内容。 当第三方 API 向元素提供内容时，此方案很有用。 由于 Blazor 不与元素交互，因此在 Blazor的元素和 DOM 的表示形式之间不存在冲突。
>
> 在下面的示例中，改变无序列表（`ul`）的内容很*危险*，因为 Blazor 与 DOM 交互以填充此元素的列表项（`<li>`）：
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
> 如果 JS 互操作改变了元素 `MyList` 的内容，并且 Blazor 尝试将差异应用到元素，则差异将与 DOM 不匹配。

就 .NET 代码而言，`ElementReference` 是一种不透明的句柄。 使用 `ElementReference` 可以执行的*唯一*操作是通过 JS 互操作将它传递给 JavaScript 代码。 当你执行此操作时，JavaScript 端代码将收到一个 `HTMLElement` 实例，该实例可用于普通 DOM Api。

例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：

*exampleJsInterop*：

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

若要调用不返回值的 JavaScript 函数，请使用 `IJSRuntime.InvokeVoidAsync`。 下面的代码通过使用捕获的 `ElementReference`调用前面的 JavaScript 函数，对用户名输入设置焦点：

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

若要使用扩展方法，请创建一个静态扩展方法，用于接收 `IJSRuntime` 实例：

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

在对象上直接调用 `Focus` 方法。 下面的示例假定 `Focus` 方法可从 `JsInteropClasses` 命名空间中获得：

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> 仅在呈现组件后填充 `username` 变量。 如果将未填充 `ElementReference` 传递给 JavaScript 代码，JavaScript 代码将接收值 `null`。 若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用[OnAfterRenderAsync 或 OnAfterRender 组件生命周期方法](xref:blazor/lifecycle#after-component-render)。

使用泛型类型并返回值时，请使用[ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1)：

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

直接在具有类型的对象上调用 `GenericMethod`。 下面的示例假定 `JsInteropClasses` 命名空间中提供了 `GenericMethod`：

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a>从 JavaScript 函数调用 .NET 方法

### <a name="static-net-method-call"></a>静态 .NET 方法调用

若要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。 传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。 异步版本是支持 Blazor 服务器方案的首选。 若要从 JavaScript 调用 .NET 方法，.NET 方法必须是公共的、静态的并且具有 `[JSInvokable]` 特性。 默认情况下，方法标识符为方法名称，但可以使用 `JSInvokableAttribute` 构造函数指定其他标识符。 当前不支持调用开放式泛型方法。

示例应用包含一个方法C# ，该方法返回 `int`的数组。 `JSInvokable` 特性应用于方法。

*Pages/JsInterop*：

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

为客户端提供的 JavaScript 调用C# .net 方法。

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

如果选择了 "**触发器 .net 静态方法 ReturnArrayAsync** " 按钮，请在浏览器的 web 开发人员工具中检查控制台输出。

控制台输出为：

```console
Array(4) [ 1, 2, 3, 4 ]
```

第四个数组值推送到 `ReturnArrayAsync`返回的数组（`data.push(4);`）。

### <a name="instance-method-call"></a>实例方法调用

还可以从 JavaScript 调用 .NET 实例方法。 从 JavaScript 调用 .NET 实例方法：

* 通过在 `DotNetObjectReference` 实例中包装 .NET 实例，将其传递给 JavaScript。 .NET 实例按对 JavaScript 的引用传递。
* 使用 `invokeMethod` 或 `invokeMethodAsync` 函数调用实例上的 .NET 实例方法。 在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。

> [!NOTE]
> 示例应用将消息记录到客户端控制台。 对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。

如果选择了 "**触发器 .net 实例方法 HelloHelper** " 按钮，则会调用 `ExampleJsInterop.CallHelloHelperSayHello` 并向方法传递 `Blazor`名称。

*Pages/JsInterop*：

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

`CallHelloHelperSayHello` 使用 `HelloHelper`的新实例调用 JavaScript 函数 `sayHello`。

*JsInteropClasses/ExampleJsInterop*：

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

该名称将传递给 `HelloHelper`的构造函数，该构造函数设置 `HelloHelper.Name` 属性。 执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。

*JsInteropClasses/HelloHelper*：

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

浏览器 web 开发人员工具中的控制台输出：

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>在类库中共享互操作代码

可在类库中包含 JS 互操作代码，这使你可以共享 NuGet 包中的代码。

类库处理在生成的程序集中嵌入 JavaScript 资源。 JavaScript 文件位于*wwwroot*文件夹中。 工具在生成库时将负责嵌入资源。

在应用程序的项目文件中引用构建的 NuGet 包的方式与引用任何 NuGet 包的方式相同。 包还原后，应用程序代码可以调入 JavaScript，就像它是C#一样。

有关更多信息，请参见<xref:blazor/class-libraries>。

## <a name="harden-js-interop-calls"></a>强化 JS 互操作调用

由于网络错误，JS 互操作可能会失败，因此应将其视为不可靠。 默认情况下，Blazor Server 应用程序一分钟后，服务器上的 JS 互操作调用会超时。 如果应用可以容忍更严格的超时，如10秒，请使用以下方法之一设置超时：

* 在 `Startup.ConfigureServices`中全局指定超时值：

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 在组件代码中，单个调用可以指定超时：

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

有关资源耗尽的详细信息，请参阅 <xref:security/blazor/server>。

## <a name="additional-resources"></a>其他资源

* [InteropComponent 示例（aspnet/AspNetCore GitHub 存储库，3.0 发布分支）](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
