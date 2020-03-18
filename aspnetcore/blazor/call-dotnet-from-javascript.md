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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="dd380-103">从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="dd380-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="dd380-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd380-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="dd380-105">Blazor 应用可通过 .NET 方法调用 JavaScript 函数，也可通过 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="dd380-106">这被称为 JavaScript 互操作（JS 互操作）   。</span><span class="sxs-lookup"><span data-stu-id="dd380-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="dd380-107">本文介绍如何从 JavaScript 调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="dd380-108">要详细了解如何通过 .NET 调用 JavaScript 函数，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="dd380-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="dd380-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dd380-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="dd380-110">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="dd380-110">Static .NET method call</span></span>

<span data-ttu-id="dd380-111">要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="dd380-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="dd380-112">传入要调用的静态方法的标识符、包含该函数的程序集的名称以及任意自变量。</span><span class="sxs-lookup"><span data-stu-id="dd380-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="dd380-113">异步版本是支持 Blazor 服务器方案的首选。</span><span class="sxs-lookup"><span data-stu-id="dd380-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="dd380-114">该 .NET 方法必须是公共的静态方法，并具有 `[JSInvokable]` 特性。</span><span class="sxs-lookup"><span data-stu-id="dd380-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="dd380-115">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="dd380-116">该示例应用包含一个 C# 方法，用于返回 `int` 数组。</span><span class="sxs-lookup"><span data-stu-id="dd380-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="dd380-117">`JSInvokable` 特性应用于该方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="dd380-118">Pages/JsInterop.razor  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="dd380-119">为客户端提供的 JavaScript 会调用 C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="dd380-120">wwwroot/exampleJsInterop.js  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="dd380-121">如果选择了“触发 .NET 静态方法 ReturnArrayAsync”按钮，请在浏览器的 Web 开发人员工具中检查控制台输出  。</span><span class="sxs-lookup"><span data-stu-id="dd380-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="dd380-122">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="dd380-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="dd380-123">第四个数组值推送到 `ReturnArrayAsync` 返回的数组 (`data.push(4);`)。</span><span class="sxs-lookup"><span data-stu-id="dd380-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="dd380-124">默认情况下，方法标识符为方法名称，但你可使用 `JSInvokableAttribute` 构造函数指定其他标识符：</span><span class="sxs-lookup"><span data-stu-id="dd380-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="dd380-125">在客户端 JavaScript 文件中：</span><span class="sxs-lookup"><span data-stu-id="dd380-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="dd380-126">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="dd380-126">Instance method call</span></span>

<span data-ttu-id="dd380-127">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="dd380-128">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="dd380-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="dd380-129">按引用向 JavaScript 传递 .NET 实例：</span><span class="sxs-lookup"><span data-stu-id="dd380-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="dd380-130">对 `DotNetObjectReference.Create` 进行静态调用。</span><span class="sxs-lookup"><span data-stu-id="dd380-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="dd380-131">在 `DotNetObjectReference` 实例中包装实例，并在 `DotNetObjectReference` 实例上调用 `Create`。</span><span class="sxs-lookup"><span data-stu-id="dd380-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="dd380-132">处置 `DotNetObjectReference` 对象（本部分稍后会展示一个示例）。</span><span class="sxs-lookup"><span data-stu-id="dd380-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="dd380-133">使用 `invokeMethod` 或 `invokeMethodAsync` 函数在实例上调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="dd380-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="dd380-134">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为自变量传递。</span><span class="sxs-lookup"><span data-stu-id="dd380-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="dd380-135">示例应用会将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="dd380-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="dd380-136">对于示例应用展示的以下示例，请在浏览器的开发人员工具中检查浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="dd380-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="dd380-137">选择“触发 .NET 实例方法 HelloHelper.SayHello”按钮时，将调用 `ExampleJsInterop.CallHelloHelperSayHello`，并将名称 `Blazor` 传递到方法  。</span><span class="sxs-lookup"><span data-stu-id="dd380-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="dd380-138">Pages/JsInterop.razor  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="dd380-139">`CallHelloHelperSayHello` 使用 `HelloHelper` 的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="dd380-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="dd380-140">JsInteropClasses/ExampleJsInterop.cs  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="dd380-141">wwwroot/exampleJsInterop.js  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="dd380-142">该名称将传递给 `HelloHelper` 的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="dd380-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="dd380-143">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="dd380-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="dd380-144">JsInteropClasses/HelloHelper.cs  ：</span><span class="sxs-lookup"><span data-stu-id="dd380-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="dd380-145">浏览器 Web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="dd380-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="dd380-146">为避免内存泄露并允许对创建 `DotNetObjectReference` 的组件进行垃圾回收，请处置类中创建 `DotNetObjectReference` 实例的对象：</span><span class="sxs-lookup"><span data-stu-id="dd380-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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
  
<span data-ttu-id="dd380-147">还可以在组件中实现上述 `ExampleJsInterop` 类中所示的模式：</span><span class="sxs-lookup"><span data-stu-id="dd380-147">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
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

## <a name="additional-resources"></a><span data-ttu-id="dd380-148">其他资源</span><span class="sxs-lookup"><span data-stu-id="dd380-148">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="dd380-149">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="dd380-149">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="dd380-150">[在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="dd380-150">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
