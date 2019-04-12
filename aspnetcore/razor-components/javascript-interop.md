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
# <a name="razor-components-javascript-interop"></a>Razor JavaScript 组件互操作

通过[Javier Calvarro Nelson](https://github.com/javiercn)， [Daniel Roth](https://github.com/danroth27)，和[Luke Latham](https://github.com/guardrex)

Razor 组件应用程序可以调用 JavaScript 函数，从.NET 和.NET 中的 JavaScript 代码的方法。

## <a name="invoke-javascript-functions-from-net-methods"></a>调用 JavaScript 函数的.NET 方法

有些的时候需要调用 JavaScript 函数的.NET 代码时。 例如，浏览器功能或从 JavaScript 库向应用程序的功能，可以公开 JavaScript 调用。

若要从.NET 调用的 JavaScript，请使用`IJSRuntime`抽象。 `InvokeAsync<T>`方法采用要以及任意数量的 JSON 可序列化参数调用的 JavaScript 函数的标识符。 函数标识符是相对于全局作用域 (`window`)。 如果你想要调用`window.someScope.someFunction`，该标识符是`someScope.someFunction`。 没有无需注册该函数之前调用它。 返回类型`T`还必须是 JSON 可序列化。

对于服务器端 ASP.NET Core Razor 组件应用程序：

* 由服务器端应用程序处理多个用户请求。 不要调用`JSRuntime.Current`组件调用 JavaScript 函数中。
* 注入`IJSRuntime`抽象和使用注入的对象发出 JavaScript 互操作调用。

下面的示例基于[TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)，实验性的基于 JavaScript 的解码器。 该示例演示如何调用一个 JavaScript 函数中的C#方法。 JavaScript 函数接受字节数组从C#方法，对数组进行解码并返回到显示的组件的文本。

内部`<head>`的元素*wwwroot/index.html*，提供一个函数来使用`TextDecoder`传递的数组进行解码：

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

此外可以从一个 JavaScript 文件加载 JavaScript 代码，例如在前面的示例所示的代码 (*.js*) 中的脚本文件引用*wwwroot/index.html*文件：

```html
<script src="exampleJsInterop.js"></script>
```

以下组件：

* 调用`ConvertArray`JavaScript 函数使用`JsRuntime`的组件按钮时 (**转换数组**) 处于选中状态。
* JavaScript 函数调用之后，则将传递的数组转换为字符串。 供显示的组件返回的字符串。

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

若要使用`IJSRuntime`抽象，采用任何以下方法之一：

* 注入`IJSRuntime`抽象到 Razor 文件 (*.razor*， *.cshtml*):

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

* 注入`IJSRuntime`抽象到一个类 (*.cs*):

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

* 有关使用动态内容生成`BuildRenderTree`，使用`[Inject]`属性：

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

在客户端的示例应用中本主题附带，两个 JavaScript 函数可供与 DOM 来接收用户输入并显示一条欢迎消息进行交互的客户端应用：

* `showPrompt` &ndash; 生成提示接受用户输入 （用户的名称），并返回给调用方的名称。
* `displayWelcome` &ndash; 将一条欢迎消息从调用方分配给具有的 DOM 对象`id`的`welcome`。

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

位置`<script>`引用的 JavaScript 文件中的标记*wwwroot/index.html*文件：

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

不放置`<script>`标记中的组件文件，因为`<script>`标记不能进行动态更新。

使用 JavaScript 的.NET 方法互操作中的函数*exampleJsInterop.js*通过调用文件`IJSRuntime.InvokeAsync<T>`。

`IJSRuntime`抽象是异步模式，以允许服务器端的方案。 如果应用在运行客户端，并且你想要调用的 JavaScript 函数以同步方式，向下转换到`IJSInProcessRuntime`，并调用`Invoke<T>`相反。 我们建议互操作的大多数 JavaScript 库，使用异步 Api，以确保的库都可在所有情况下，客户端或服务器端。

示例应用包含用于演示 JavaScript 互操作的组件。 组件：

* 接收通过 JavaScript 提示用户输入。
* 返回对组件进行处理的文本。
* 调用与 DOM 以显示一条欢迎消息进行交互的第二个 JavaScript 函数。

*Pages/JSInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. 当`TriggerJsPrompt`选择该组件的执行**触发器 JavaScript 提示**按钮，JavaScript`showPrompt`函数中提供*wwwroot/exampleJsInterop.js*文件调用。
1. `showPrompt`函数接受用户输入 （用户的名称），这是 HTML 编码并返回到该组件。 该组件将用户的名称存储在本地变量中， `name`。
1. 将字符串存储在`name`合并到一条欢迎消息，这传递给 JavaScript 函数， `displayWelcome`，它的标题标记将呈现的欢迎消息。

## <a name="capture-references-to-elements"></a>捕获对元素的引用

某些[JavaScript 互操作](xref:razor-components/javascript-interop)情况下，需要对 HTML 元素的引用。 例如，用户界面库可能需要元素引用进行初始化，或者可能需要在元素上，调用类似于命令的 Api，例如`focus`或`play`。

你可以通过添加捕获对组件中的 HTML 元素的引用`ref`属性的 HTML 元素，然后定义类型的字段`ElementRef`其名称与匹配的值`ref`属性。

下面的示例显示了捕获用户名输入元素的引用：

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> 不要**不**作为填充 DOM 的一种方法使用捕获的元素引用 执行此操作可能会影响声明性呈现模型。

就.NET 代码而言，`ElementRef`是不透明的句柄。 *仅*可以用做的事情`ElementRef`是通过 JavaScript 互操作将通过其传递给 JavaScript 代码。 当你执行此操作时，在 JavaScript 端代码接收`HTMLElement`实例，它可以与普通的 DOM Api 配合使用。

例如，下面的代码定义一个.NET 扩展方法，使将焦点设置在元素上：

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

使用`IJSRuntime.InvokeAsync<T>`，并调用`exampleJsFunctions.focusElement`与`ElementRef`能够将精力集中元素：

[!code-cshtml[](javascript-interop/samples_snapshot/component1.cshtml?highlight=1,3,7,11-12)]

若要使用的扩展方法可以将精力集中元素，创建一个静态扩展方法可接收`IJSRuntime`实例：

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

直接在对象上调用方法。 下面的示例假定静态`Focus`方法是可从`JsInteropClasses`命名空间：

[!code-cshtml[](javascript-interop/samples_snapshot/component2.cshtml?highlight=1,4,8,12)]

> [!IMPORTANT]
> `username`组件呈现并包括其输出后，仅填充变量`>`元素。 如果你尝试传递即便`ElementRef`向 JavaScript 代码的 JavaScript 代码接收`null`。 操作元素的引用，该组件已经完成呈现 （将在元素上设置初始焦点） 使用后`OnAfterRenderAsync`或`OnAfterRender`[组件生命周期方法](xref:razor-components/components#lifecycle-methods)。

## <a name="invoke-net-methods-from-javascript-functions"></a>调用 JavaScript 函数中的.NET 方法

### <a name="static-net-method-call"></a>静态.NET 方法调用

若要调用静态.NET 方法从 JavaScript，请使用`DotNet.invokeMethod`或`DotNet.invokeMethodAsync`函数。 传入你想要调用的函数，并且任何自变量包含的程序集名称的静态方法的标识符。 首选的异步版本来支持服务器端的方案。 若要成为可从 JavaScript 调用，.NET 方法必须是公共、 静态的并使用经过修饰`[JSInvokable]`。 默认情况下，方法标识符是方法名称，但可以指定不同的标识符使用`JSInvokableAttribute`构造函数。 调用开放式泛型方法当前不支持。

示例应用包含C#方法返回的数组`int`s。 该方法用修饰`JSInvokable`属性。

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop2&highlight=7-11)]

客户端的 JavaScript 调用C#.NET 方法。

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

当**触发器.NET 静态方法 ReturnArrayAsync**按钮处于选中状态，请查看浏览器的 web 开发人员工具中的控制台输出。

控制台输出为：

```console
Array(4) [ 1, 2, 3, 4 ]
```

第四个数组值推送到的数组 (`data.push(4);`) 返回的`ReturnArrayAsync`。

### <a name="instance-method-call"></a>实例方法调用

此外可以从 JavaScript 调用.NET 实例方法。 若要调用的 JavaScript 的.NET 实例方法，第一次的实例传递给.NET 到 JavaScript 通过包装在`DotNetObjectRef`实例。 .NET 实例通过引用传递给 JavaScript，并可调用实例使用的.NET 实例方法`invokeMethod`或`invokeMethodAsync`函数。 调用其他.NET 方法从 JavaScript 时，还可以作为参数传递.NET 实例。

> [!NOTE]
> 示例应用程序将消息记录到客户端的控制台。 对于以下示例所演示的示例应用中，检查浏览器的开发人员工具中的浏览器的控制台输出。

当**触发器.NET 实例方法 HelloHelper.SayHello**选择按钮时，`ExampleJsInterop.CallHelloHelperSayHello`调用，并将传递一个名称， `Blazor`，给该方法。

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` 调用 JavaScript 函数`sayHello`新实例的`HelloHelper`。

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

名称传递给`HelloHelper`的构造函数中，这会设置`HelloHelper.Name`属性。 当 JavaScript 函数`sayHello`执行时，`HelloHelper.SayHello`返回`Hello, {Name}!`消息，通过 JavaScript 函数写入到控制台。

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

控制台输出中浏览器的 web 开发人员工具：

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a>共享组件 Razor 类库中的互操作代码

可以在 Razor 组件的类库中包含 JavaScript 互操作代码 (`dotnet new razorclasslib`)，这样您就可以共享 NuGet 程序包中的代码。

Razor 组件类库处理生成的程序集中嵌入的 JavaScript 资源。 JavaScript 文件将放入*wwwroot*文件夹。 工具将负责生成库时，嵌入资源。

就像引用任何普通的 NuGet 包，生成的 NuGet 程序包引用在项目文件中的应用程序。 就像还原应用程序后，应用程序代码可以调用到 JavaScript C#。

有关详细信息，请参阅 <xref:razor-components/class-libraries>。
