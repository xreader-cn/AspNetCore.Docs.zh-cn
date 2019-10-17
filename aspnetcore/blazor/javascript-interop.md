---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor apps 中的 JavaScript 调用来自 .NET 和 .NET 方法的 JavaScript 函数。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/javascript-interop
ms.openlocfilehash: b4776a20c6da6c722d2c057d19863c570f530a21
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391067"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 互操作

作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="invoke-javascript-functions-from-net-methods"></a>从 .NET 方法调用 JavaScript 函数

有时需要 .NET 代码才能调用 JavaScript 函数。 例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。

若要从 .NET 调入 JavaScript，请使用 `IJSRuntime` 抽象。 @No__t-0 方法获取要调用的 JavaScript 函数的标识符，以及任意数量的 JSON 可序列化参数。 函数标识符相对于全局范围（`window`）。 如果要调用 `window.someScope.someFunction`，标识符 @no__t 为-1。 无需在调用函数之前注册它。 返回类型 `T` 也必须是 JSON 可序列化的。

对于 Blazor 服务器应用：

* Blazor 服务器应用处理多个用户请求。 请勿在组件中调用 `JSRuntime.Current` 来调用 JavaScript 函数。
* 注入 `IJSRuntime` 抽象，并使用注入的对象发出 JavaScript 互操作调用。
* 预呈现 Blazor 的应用时，无法调用 JavaScript，因为尚未建立与浏览器的连接。 有关详细信息，请参阅 "在预[呈现 Blazor 应用时检测](#detect-when-a-blazor-app-is-prerendering)" 部分。

下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。 该示例演示如何从C#方法调用 JavaScript 函数。 JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。

在*wwwroot/index.html* （Blazor WebAssembly）或*Pages/_Host* （Blazor 服务器）的 `<head>` 元素内，提供一个函数，该函数使用 @no__t 来解码传递的数组：

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 选择组件按钮（**转换数组**）时，使用 `JsRuntime` 调用 `ConvertArray` JavaScript 函数。
* 调用 JavaScript 函数后，传递的数组将转换为字符串。 将字符串返回到要显示的组件。

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

若要使用 `IJSRuntime` 抽象，请采用以下方法之一：

* 将 `IJSRuntime` 抽象插入 Razor 组件（*razor*）：

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* 将 `IJSRuntime` 抽象插入到类（ *.cs*）中：

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* 对于使用[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)的动态内容生成，请使用 `[Inject]` 属性：

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息：

* `showPrompt` &ndash; 将生成一个接收用户输入（用户的名称）的提示，并将名称返回给调用方。
* `displayWelcome` &ndash; 将一个欢迎消息从调用方分配到 @no__t 为 `id` 的 DOM 对象。

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

将引用 JavaScript 文件的 @no__t 0 标记放置在*wwwroot/index.html*文件（Blazor WebAssembly）或*Pages/_Host*文件（Blazor 服务器）中。

*wwwroot/index.html* （Blazor WebAssembly）：

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host* （Blazor 服务器）：

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

请勿在组件文件中放置 @no__t 的标记，因为 @no__t 的标记无法动态更新。

.NET 方法通过调用 `IJSRuntime.InvokeAsync<T>`，与*exampleJsInterop*文件中的 JavaScript 函数互操作。

@No__t-0 抽象是异步的，以允许 Blazor 服务器方案。 如果应用是 Blazor WebAssembly 应用，并且你想要同步调用 JavaScript 函数，则向下转换到 `IJSInProcessRuntime` 并改为调用 `Invoke<T>`。 建议大多数 JavaScript 互操作库使用异步 Api，以确保库在所有方案中都可用。

该示例应用包含一个用于演示 JavaScript 互操作的组件。 组件：

* 通过 JavaScript 提示接收用户输入。
* 将文本返回到组件进行处理。
* 调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。

*Pages/JSInterop*：

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. 通过选择组件的 "**触发器 JavaScript" 提示**按钮执行 `TriggerJsPrompt` 时，将调用*wwwroot/exampleJsInterop*文件中提供的 JavaScript `showPrompt` 功能。
1. @No__t-0 函数接受经过 HTML 编码并返回到组件的用户输入（用户的名称）。 组件将用户的名称存储在本地变量中，`name`。
1. 存储在 `name` 中的字符串合并为一个欢迎消息，该消息将被传递给 JavaScript 函数，`displayWelcome`，这会将欢迎消息呈现到标题标记中。

## <a name="call-a-void-javascript-function"></a>调用 void JavaScript 函数

返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数在 `IJSRuntime.InvokeVoidAsync` 的情况中调用。

## <a name="detect-when-a-blazor-app-is-prerendering"></a>检测何时预呈现 Blazor 应用
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些[JavaScript 互操作](xref:blazor/javascript-interop)方案需要引用 HTML 元素。 例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素（例如 `focus` 或 `play`）上调用类似命令的 Api。

使用以下方法捕获对组件中 HTML 元素的引用：

* 将 `@ref` 特性添加到 HTML 元素。
* 定义类型 `ElementReference` 的字段，其名称与 @no__t 属性的值匹配。

下面的示例演示捕获对 `username` @no__t 元素的引用：

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> 不要**使用捕获**的元素引用作为填充 DOM 的方式。 这样做可能会干扰声明性呈现模型。

就 .NET 代码而言，@no__t 0 是不透明的句柄。 使用 `ElementReference` 可以执行的*唯一*操作是通过 javascript 互操作将它传递给 javascript 代码。 当你执行此操作时，JavaScript 端代码将收到一个 @no__t 为0的实例，该实例可用于普通 DOM Api。

例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：

*exampleJsInterop*：

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

使用 `IJSRuntime.InvokeAsync<T>` 并使用 `ElementReference` 调用 @no__t 以使元素聚焦：

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

若要使用扩展方法集中元素，请创建一个静态扩展方法，用于接收 @no__t 实例：

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

在对象上直接调用方法。 下面的示例假定 @no__t 命名空间中提供了 static `Focus` 方法：

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> 仅在呈现组件后，才填充 `username` 变量。 如果向 JavaScript 代码传递了未填充 `ElementReference`，JavaScript 代码将接收值 `null`。 若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用 @no__t 0 或 @no__t[组件生命周期方法](xref:blazor/components#lifecycle-methods)。

## <a name="invoke-net-methods-from-javascript-functions"></a>从 JavaScript 函数调用 .NET 方法

### <a name="static-net-method-call"></a>静态 .NET 方法调用

若要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。 传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。 异步版本是支持 Blazor 服务器方案的首选。 若要从 JavaScript 调用 .NET 方法，.NET 方法必须是公共的且为静态的，并具有 `[JSInvokable]` 属性。 默认情况下，方法标识符为方法名称，但可以使用 `JSInvokableAttribute` 构造函数指定其他标识符。 当前不支持调用开放式泛型方法。

示例应用包含一个方法C# ，该方法返回 @no__t 1 的数组。 @No__t-0 特性应用于方法。

*Pages/JsInterop*：

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

为客户端提供的 JavaScript 调用C# .net 方法。

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

如果选择了 "**触发器 .net 静态方法 ReturnArrayAsync** " 按钮，请在浏览器的 web 开发人员工具中检查控制台输出。

控制台输出为：

```console
Array(4) [ 1, 2, 3, 4 ]
```

第四个数组值推送到 @no__t 返回的数组（@no__t）。

### <a name="instance-method-call"></a>实例方法调用

还可以从 JavaScript 调用 .NET 实例方法。 从 JavaScript 调用 .NET 实例方法：

* 通过在 `DotNetObjectReference` 实例中包装 .NET 实例，将其传递给 JavaScript。 .NET 实例按对 JavaScript 的引用传递。
* 使用 @no__t 0 或 `invokeMethodAsync` 函数调用实例上的 .NET 实例方法。 在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。

> [!NOTE]
> 示例应用将消息记录到客户端控制台。 对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。

如果选择了**触发器 .net 实例方法 HelloHelper SayHello**按钮，则会调用 `ExampleJsInterop.CallHelloHelperSayHello`，并将名称 `Blazor` 传递给方法。

*Pages/JsInterop*：

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` 用 `HelloHelper` 的新实例调用 JavaScript 函数 @no__t。

*JsInteropClasses/ExampleJsInterop*：

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

该名称将传递给 @no__t 0 的构造函数，该构造函数设置 @no__t 的属性。 当执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。

*JsInteropClasses/HelloHelper*：

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

浏览器 web 开发人员工具中的控制台输出：

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>在类库中共享互操作代码

JavaScript 互操作代码可以包含在类库中，这使你可以共享 NuGet 包中的代码。

类库处理在生成的程序集中嵌入 JavaScript 资源。 JavaScript 文件位于*wwwroot*文件夹中。 工具在生成库时将负责嵌入资源。

在应用程序的项目文件中引用构建的 NuGet 包的方式与引用任何 NuGet 包的方式相同。 包还原后，应用程序代码可以调入 JavaScript，就像它是C#一样。

有关更多信息，请参见<xref:blazor/class-libraries>。

## <a name="harden-js-interop-calls"></a>强化 JS 互操作调用

由于网络错误，JS 互操作可能会失败，因此应将其视为不可靠。 默认情况下，Blazor 服务器应用程序在一分钟后就会超时服务器上的 JS 互操作调用。 如果应用可以容忍更严格的超时，如10秒，请使用以下方法之一设置超时：

* 在 @no__t 中全局地指定超时：

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
