---
title: 从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法
author: guardrex
description: 了解如何在 Blazor 应用中通过 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: f4964341e261c65269eedafbbd6e676c1054f427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647406"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法

作者：[Javier Calvarro Nelson](https://github.com/javiercn)[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 应用可通过 .NET 方法调用 JavaScript 函数，也可通过 JavaScript 函数调用 .NET 方法。 这被称为 JavaScript 互操作（JS 互操作）   。

本文介绍如何从 JavaScript 调用 .NET 方法。 要详细了解如何通过 .NET 调用 JavaScript 函数，请参阅 <xref:blazor/call-javascript-from-dotnet>。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="static-net-method-call"></a>静态 .NET 方法调用

要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。 传入要调用的静态方法的标识符、包含该函数的程序集的名称以及任意自变量。 异步版本是支持 Blazor 服务器方案的首选。 该 .NET 方法必须是公共的静态方法，并具有 `[JSInvokable]` 特性。 当前不支持调用开放式泛型方法。

该示例应用包含一个 C# 方法，用于返回 `int` 数组。 `JSInvokable` 特性应用于该方法。

Pages/JsInterop.razor  ：

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

为客户端提供的 JavaScript 会调用 C# .net 方法。

wwwroot/exampleJsInterop.js  ：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

如果选择了“触发 .NET 静态方法 ReturnArrayAsync”按钮，请在浏览器的 Web 开发人员工具中检查控制台输出  。

控制台输出为：

```console
Array(4) [ 1, 2, 3, 4 ]
```

第四个数组值推送到 `ReturnArrayAsync` 返回的数组 (`data.push(4);`)。

默认情况下，方法标识符为方法名称，但你可使用 `JSInvokableAttribute` 构造函数指定其他标识符：

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

在客户端 JavaScript 文件中：

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>实例方法调用

还可以从 JavaScript 调用 .NET 实例方法。 从 JavaScript 调用 .NET 实例方法：

* 按引用向 JavaScript 传递 .NET 实例：
  * 对 `DotNetObjectReference.Create` 进行静态调用。
  * 在 `DotNetObjectReference` 实例中包装实例，并在 `DotNetObjectReference` 实例上调用 `Create`。 处置 `DotNetObjectReference` 对象（本部分稍后会展示一个示例）。
* 使用 `invokeMethod` 或 `invokeMethodAsync` 函数在实例上调用 .NET 实例方法。 在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为自变量传递。

> [!NOTE]
> 示例应用会将消息记录到客户端控制台。 对于示例应用展示的以下示例，请在浏览器的开发人员工具中检查浏览器的控制台输出。

选择“触发 .NET 实例方法 HelloHelper.SayHello”按钮时，将调用 `ExampleJsInterop.CallHelloHelperSayHello`，并将名称 `Blazor` 传递到方法  。

Pages/JsInterop.razor  ：

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

`CallHelloHelperSayHello` 使用 `HelloHelper` 的新实例调用 JavaScript 函数 `sayHello`。

JsInteropClasses/ExampleJsInterop.cs  ：

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

wwwroot/exampleJsInterop.js  ：

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

该名称将传递给 `HelloHelper` 的构造函数，该构造函数设置 `HelloHelper.Name` 属性。 执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。

JsInteropClasses/HelloHelper.cs  ：

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

浏览器 Web 开发人员工具中的控制台输出：

```console
Hello, Blazor!
```

为避免内存泄露并允许对创建 `DotNetObjectReference` 的组件进行垃圾回收，请处置类中创建 `DotNetObjectReference` 实例的对象：

```csharp
public class ExampleJsInterop : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private DotNetObjectReference<HelloHelper> _objRef;

    public ExampleJsInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public ValueTask<string> CallHelloHelperSayHello(string name)
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper(name));

        return _jsRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```
  
还可以在组件中实现上述 `ExampleJsInterop` 类中所示的模式：
  
```razor
@page "/JSInteropComponent"
@using BlazorSample.JsInteropClasses
@implements IDisposable
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    private DotNetObjectReference<HelloHelper> _objRef;

    public async Task TriggerNetInstanceMethod()
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

        await JSRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>其他资源

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
