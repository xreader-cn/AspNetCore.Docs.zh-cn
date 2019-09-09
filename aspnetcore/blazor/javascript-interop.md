---
title: ASP.NET Core Blazor JavaScript 互操作
author: guardrex
description: 了解如何从 Blazor apps 中的 JavaScript 调用来自 .NET 和 .NET 方法的 JavaScript 函数。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/javascript-interop
ms.openlocfilehash: 4e2c979971f8f550af4aa9653880bfd1e5fae731
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800307"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 互操作

作者： [Javier Calvarro 使用](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)

Blazor 应用可从 JavaScript 代码调用 .NET 和 .NET 方法中的 JavaScript 函数。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="invoke-javascript-functions-from-net-methods"></a>从 .NET 方法调用 JavaScript 函数

有时需要 .NET 代码才能调用 JavaScript 函数。 例如，JavaScript 调用可以向应用公开 JavaScript 库中的浏览器功能或功能。

若要从 .net 调入 JavaScript，请`IJSRuntime`使用抽象。 `InvokeAsync<T>`方法采用 JavaScript 函数的标识符，该标识符可与任意数量的 JSON 可序列化参数一起调用。 函数标识符是相对于全局范围（`window`）的。 如果要调用`window.someScope.someFunction`，则标识符为`someScope.someFunction`。 无需在调用函数之前注册它。 返回类型`T`也必须是 JSON 可序列化的。

对于服务器端应用：

* 服务器端应用处理多个用户请求。 不要在`JSRuntime.Current`组件中调用来调用 JavaScript 函数。
* `IJSRuntime`注入抽象并使用注入的对象发出 JavaScript 互操作调用。
* 预呈现 Blazor 的应用时，无法调用 JavaScript，因为尚未建立与浏览器的连接。 有关详细信息，请参阅 "在预[呈现 Blazor 应用时检测](#detect-when-a-blazor-app-is-prerendering)" 部分。

下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)（基于实验性 JavaScript 的解码器）。 该示例演示如何从C#方法调用 JavaScript 函数。 JavaScript 函数从C#方法接受字节数组，对数组进行解码，并将文本返回给组件以供显示。

在 wwwroot/index.html `<head>` （Blazor客户端）或*Pages/_Host* （Blazor 服务器端）的元素中`TextDecoder` ，提供用于对传递的数组进行解码的函数：

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

JavaScript 代码（如前面的示例中所示的代码）也可以从 JavaScript 文件（ *.js*）加载，其中包含对脚本文件的引用：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 在选择组件按钮（ `JsRuntime` **转换数组**）时使用调用JavaScript函数。`ConvertArray`
* 调用 JavaScript 函数后，传递的数组将转换为字符串。 将字符串返回到要显示的组件。

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

若要使用`IJSRuntime`抽象，请采用以下任一方法：

* 将抽象注入 razor 组件（razor）： `IJSRuntime`

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* 将抽象注入到类（.cs）： `IJSRuntime`

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* 对于使用[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)的动态内容生成，请`[Inject]`使用属性：

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

本主题附带的客户端示例应用程序提供了两个 JavaScript 函数，可用于与 DOM 交互以接收用户输入并显示欢迎消息的客户端应用程序：

* `showPrompt`&ndash;生成一个提示，以接受用户输入（用户名）并将名称返回给调用方。
* `displayWelcome`将来自调用方的欢迎消息分配给具有的`welcome`一个`id` DOM 对象。 &ndash;

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

将引用 JavaScript 文件的 标记放在wwwroot/index.html文件（Blazor客户端）或Pages/_Host文件中（Blazor服务器端`<script>` ）。

*wwwroot/index.html*（Blazor 客户端）：

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host*（Blazor 服务器端）：

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

请勿在组件`<script>`文件中放置标记， `<script>`因为标记无法动态更新。

.NET 方法通过调用`IJSRuntime.InvokeAsync<T>`来与*ExampleJsInterop*文件中的 JavaScript 函数互操作。

`IJSRuntime`抽象是异步的，以允许服务器端方案。 如果应用程序运行客户端，并且你希望同步调用 JavaScript 函数，则转到`IJSInProcessRuntime`和调用。 `Invoke<T>` 建议大多数 JavaScript 互操作库使用异步 Api，以确保所有方案、客户端或服务器端的库可用。

该示例应用包含一个用于演示 JavaScript 互操作的组件。 组件：

* 通过 JavaScript 提示接收用户输入。
* 将文本返回到组件进行处理。
* 调用第二个 JavaScript 函数，该函数与 DOM 交互以显示欢迎消息。

*Pages/JSInterop*：

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. 通过`TriggerJsPrompt`选择组件的 "**触发器 JavaScript" 提示**按钮执行时，将调用`showPrompt` *wwwroot/exampleJsInterop*文件中提供的 JavaScript 函数。
1. `showPrompt`函数接受 HTML 编码并返回到组件的用户输入（用户的名称）。 组件将用户的名称存储在本地变量`name`中。
1. 存储在中`name`的字符串合并为一个欢迎消息，该消息将传递给一个 JavaScript `displayWelcome`函数，该函数将欢迎消息呈现为标题标记。

## <a name="call-a-void-javascript-function"></a>调用 void JavaScript 函数

返回[void （0）/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)或[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函数`IJSRuntime.InvokeAsync<object>`将在返回`null`的中调用。

## <a name="detect-when-a-blazor-app-is-prerendering"></a>检测何时预呈现 Blazor 应用
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些[JavaScript 互操作](xref:blazor/javascript-interop)方案需要引用 HTML 元素。 例如，UI 库可能需要用于初始化的元素引用，或者可能需要在元素上调用类似于命令的 api，例如`focus`或。 `play`

使用以下方法捕获对组件中 HTML 元素的引用：

* `@ref`将属性添加到 HTML 元素。
* 定义其名称与`ElementReference` `@ref`属性的值相匹配的类型的字段。

下面的示例演示捕获对`username` `<input>`元素的引用：

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> 当 Blazor 与所引用的元素交互时，**不要使用捕获**的元素引用作为填充或操作 DOM 的方式。 这样做可能会干扰声明性呈现模型。

就 .net 代码而言， `ElementReference`是不透明的句柄。 *唯一*可以执行的操作`ElementReference`是通过 javascript 互操作将它传递给 javascript 代码。 当你执行此操作时，JavaScript 端代码将收到`HTMLElement`一个实例，该实例可与普通 DOM api 一起使用。

例如，下面的代码定义了一个 .NET 扩展方法，该方法可在元素上设置焦点：

*exampleJsInterop*：

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

使用`IJSRuntime.InvokeAsync<T>`并调用`exampleJsFunctions.focusElement` 来集中元素：`ElementReference`

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

若要使用扩展方法集中元素，请创建一个接收`IJSRuntime`实例的静态扩展方法：

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

在对象上直接调用方法。 下面的示例假定静态`Focus`方法可`JsInteropClasses`从命名空间中获得：

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> 仅在呈现组件后填充变量。`username` 如果将未填充`ElementReference`传递给 javascript 代码，javascript 代码将接收`null`值。 若要在组件完成呈现后操作元素引用（若要设置元素的初始焦点），请使用`OnAfterRenderAsync`或`OnAfterRender` [组件生命周期方法](xref:blazor/components#lifecycle-methods)。

## <a name="invoke-net-methods-from-javascript-functions"></a>从 JavaScript 函数调用 .NET 方法

### <a name="static-net-method-call"></a>静态 .NET 方法调用

若要从 JavaScript 调用静态 .net 方法，请使用`DotNet.invokeMethod`或`DotNet.invokeMethodAsync`函数。 传入要调用的静态方法的标识符、包含该函数的程序集的名称和任何自变量。 异步版本是支持服务器端方案的首选。 若要从 JavaScript 调用 .net 方法，.net 方法必须是公共的，静态的，并具有`[JSInvokable]`属性。 默认情况下，方法标识符为方法名称，但可以使用`JSInvokableAttribute`构造函数指定其他标识符。 当前不支持调用开放式泛型方法。

该示例应用包含一个C#返回数组的`int`方法。 `JSInvokable`特性应用于方法。

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

第四个数组值推送到由`data.push(4);` `ReturnArrayAsync`返回的数组（）。

### <a name="instance-method-call"></a>实例方法调用

还可以从 JavaScript 调用 .NET 实例方法。 从 JavaScript 调用 .NET 实例方法：

* 通过在`DotNetObjectReference`实例中包装 .net 实例，将其传递给 JavaScript。 .NET 实例按对 JavaScript 的引用传递。
* 使用`invokeMethod` 或`invokeMethodAsync`函数调用实例上的 .net 实例方法。 在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为参数传递。

> [!NOTE]
> 示例应用将消息记录到客户端控制台。 对于示例应用演示的以下示例，请在浏览器的开发人员工具中查看浏览器的控制台输出。

如果选择了**触发器 .net 实例方法 HelloHelper SayHello**按钮， `ExampleJsInterop.CallHelloHelperSayHello`将调用并向方法`Blazor`传递一个名称。

*Pages/JsInterop*：

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello`使用的新`HelloHelper`实例`sayHello`调用 JavaScript 函数。

*JsInteropClasses/ExampleJsInterop*：

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*：

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

该名称将传递给`HelloHelper`的构造函数，该构造`HelloHelper.Name`函数设置属性。 执行 javascript 函数`sayHello`时， `HelloHelper.SayHello`将返回`Hello, {Name}!`消息，该消息由 JavaScript 函数写入控制台。

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

有关详细信息，请参阅 <xref:blazor/class-libraries> 。
