---
title: 从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法
author: guardrex
description: 了解如何在 Blazor 应用中通过 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 45ddcc9e006df2c5e86a7859efc76882b269a496
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280388"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="cac74-103">从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="cac74-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="cac74-104">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="cac74-105">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="cac74-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="cac74-106">本文介绍如何从 JavaScript 调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-106">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="cac74-107">要详细了解如何通过 .NET 调用 JavaScript 函数，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="cac74-107">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="cac74-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cac74-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="cac74-109">将 JS 文件（`<script>` 标记）添加到 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中的 `</body>` 结束标记前。</span><span class="sxs-lookup"><span data-stu-id="cac74-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="cac74-110">确保在 Blazor framework JS 文件之前包含带有 JS 互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="cac74-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="cac74-111">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="cac74-111">Static .NET method call</span></span>

<span data-ttu-id="cac74-112">要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="cac74-112">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="cac74-113">传入要调用的静态方法的标识符、包含该函数的程序集的名称以及任意自变量。</span><span class="sxs-lookup"><span data-stu-id="cac74-113">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="cac74-114">异步版本是支持 Blazor Server 方案的首选。</span><span class="sxs-lookup"><span data-stu-id="cac74-114">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="cac74-115">.NET 方法必须是公共的静态方法，并且包含 [`[JSInvokable]` 特性](xref:Microsoft.JSInterop.JSInvokableAttribute)。</span><span class="sxs-lookup"><span data-stu-id="cac74-115">The .NET method must be public, static, and have the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute).</span></span> <span data-ttu-id="cac74-116">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-116">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="cac74-117">该示例应用包含一个 C# 方法，用于返回 `int` 数组。</span><span class="sxs-lookup"><span data-stu-id="cac74-117">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="cac74-118">[`[JSInvokable]` 特性](xref:Microsoft.JSInterop.JSInvokableAttribute)应用于该方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-118">The [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) is applied to the method.</span></span>

<span data-ttu-id="cac74-119">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-119">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="cac74-120">为客户端提供的 JavaScript 会调用 C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-120">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="cac74-121">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="cac74-121">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="cac74-122">如果选择了“`Trigger .NET static method ReturnArrayAsync`”按钮，请在浏览器的 Web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="cac74-122">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="cac74-123">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="cac74-123">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="cac74-124">第四个数组值推送到 `ReturnArrayAsync` 返回的数组 (`data.push(4);`)。</span><span class="sxs-lookup"><span data-stu-id="cac74-124">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="cac74-125">默认情况下，方法标识符是方法名称，但你也可以使用 [`[JSInvokable]` 特性](xref:Microsoft.JSInterop.JSInvokableAttribute)构造函数来指定其他标识符：</span><span class="sxs-lookup"><span data-stu-id="cac74-125">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="cac74-126">在客户端 JavaScript 文件中：</span><span class="sxs-lookup"><span data-stu-id="cac74-126">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="cac74-127">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-127">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="cac74-128">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="cac74-128">Instance method call</span></span>

<span data-ttu-id="cac74-129">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-129">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="cac74-130">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="cac74-130">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="cac74-131">按引用向 JavaScript 传递 .NET 实例：</span><span class="sxs-lookup"><span data-stu-id="cac74-131">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="cac74-132">对 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> 进行静态调用。</span><span class="sxs-lookup"><span data-stu-id="cac74-132">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="cac74-133">在 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例中包装实例，并在 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例上调用 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A>。</span><span class="sxs-lookup"><span data-stu-id="cac74-133">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="cac74-134">处置 <xref:Microsoft.JSInterop.DotNetObjectReference> 对象（本部分稍后会展示一个示例）。</span><span class="sxs-lookup"><span data-stu-id="cac74-134">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="cac74-135">使用 `invokeMethod` 或 `invokeMethodAsync` 函数在实例上调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-135">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="cac74-136">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为自变量传递。</span><span class="sxs-lookup"><span data-stu-id="cac74-136">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="cac74-137">示例应用会将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="cac74-137">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="cac74-138">对于示例应用展示的以下示例，请在浏览器的开发人员工具中检查浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="cac74-138">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="cac74-139">如果选择了“`Trigger .NET instance method HelloHelper.SayHello`”按钮，则 `ExampleJsInterop.CallHelloHelperSayHello` 会被调用并将名称 `Blazor` 传递给方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-139">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="cac74-140">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-140">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="cac74-141">`CallHelloHelperSayHello` 使用 `HelloHelper` 的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="cac74-141">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="cac74-142">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="cac74-142">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="cac74-143">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="cac74-143">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="cac74-144">该名称将传递给 `HelloHelper` 的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="cac74-144">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="cac74-145">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="cac74-145">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="cac74-146">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="cac74-146">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="cac74-147">浏览器 Web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="cac74-147">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="cac74-148">为避免内存泄露并允许对创建 <xref:Microsoft.JSInterop.DotNetObjectReference> 的组件进行垃圾回收，请采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="cac74-148">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="cac74-149">处置类中创建了 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例的对象：</span><span class="sxs-lookup"><span data-stu-id="cac74-149">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="cac74-150">还可以在组件中实现上述 `ExampleJsInterop` 类中所示的模式：</span><span class="sxs-lookup"><span data-stu-id="cac74-150">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="cac74-151">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-151">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="cac74-152">如果组件或类不处置 <xref:Microsoft.JSInterop.DotNetObjectReference>，请通过调用 `.dispose()` 在客户端上处置该对象：</span><span class="sxs-lookup"><span data-stu-id="cac74-152">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="cac74-153">组件实例方法调用</span><span class="sxs-lookup"><span data-stu-id="cac74-153">Component instance method call</span></span>

<span data-ttu-id="cac74-154">要调用组件的 .NET 方法，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cac74-154">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="cac74-155">使用 `invokeMethod` 或 `invokeMethodAsync` 函数对组件执行静态方法调用。</span><span class="sxs-lookup"><span data-stu-id="cac74-155">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="cac74-156">组件的静态方法将其实例方法调用包装为已调用的 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="cac74-156">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="cac74-157">对于多名用户可能同时使用同一组件的 Blazor Server 应用，请使用帮助程序类来调用实例方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-157">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="cac74-158">有关详细信息，请参阅[组件实例方法帮助程序类](#component-instance-method-helper-class)部分。</span><span class="sxs-lookup"><span data-stu-id="cac74-158">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="cac74-159">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="cac74-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="cac74-160">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cac74-161">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-161">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="cac74-162">若要向实例方法传递参数：</span><span class="sxs-lookup"><span data-stu-id="cac74-162">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="cac74-163">向 JS 方法调用添加参数。</span><span class="sxs-lookup"><span data-stu-id="cac74-163">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="cac74-164">在下面的示例中，一个名称被传递给方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-164">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="cac74-165">可根据需要将其他参数添加到列表。</span><span class="sxs-lookup"><span data-stu-id="cac74-165">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="cac74-166">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-166">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="cac74-167">向参数的 <xref:System.Action> 提供正确的类型。</span><span class="sxs-lookup"><span data-stu-id="cac74-167">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="cac74-168">向 C# 方法提供参数列表。</span><span class="sxs-lookup"><span data-stu-id="cac74-168">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="cac74-169">使用参数 (`action.Invoke(name)`) 调用 <xref:System.Action> (`UpdateMessage`)。</span><span class="sxs-lookup"><span data-stu-id="cac74-169">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="cac74-170">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-170">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="cac74-171">选择“调用 JS 方法”按钮时输出 `message`：</span><span class="sxs-lookup"><span data-stu-id="cac74-171">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="cac74-172">组件实例方法帮助程序类</span><span class="sxs-lookup"><span data-stu-id="cac74-172">Component instance method helper class</span></span>

<span data-ttu-id="cac74-173">帮助程序类用于将实例方法作为 <xref:System.Action> 进行调用。</span><span class="sxs-lookup"><span data-stu-id="cac74-173">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="cac74-174">帮助程序类在以下情况中非常有用：</span><span class="sxs-lookup"><span data-stu-id="cac74-174">Helper classes are useful when:</span></span>

* <span data-ttu-id="cac74-175">同一类型的多个组件呈现在同一页上。</span><span class="sxs-lookup"><span data-stu-id="cac74-175">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="cac74-176">使用 Blazor Server 应用，其中多名用户可能同时使用某个组件。</span><span class="sxs-lookup"><span data-stu-id="cac74-176">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="cac74-177">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="cac74-177">In the following example:</span></span>

* <span data-ttu-id="cac74-178">`JSInteropExample` 组件包含若干 `ListItem` 组件。</span><span class="sxs-lookup"><span data-stu-id="cac74-178">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="cac74-179">每个 `ListItem` 组件都由一个消息和一个按钮组成。</span><span class="sxs-lookup"><span data-stu-id="cac74-179">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="cac74-180">选择 `ListItem` 组件按钮后，`ListItem` 的 `UpdateMessage` 方法会更改列表项文本并隐藏该按钮。</span><span class="sxs-lookup"><span data-stu-id="cac74-180">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="cac74-181">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="cac74-181">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="cac74-182">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-182">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cac74-183">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="cac74-183">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="cac74-184">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="cac74-184">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="cac74-185">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-185">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="cac74-186">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="cac74-186">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="cac74-187">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="cac74-187">Avoid circular object references</span></span>

<span data-ttu-id="cac74-188">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="cac74-188">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="cac74-189">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="cac74-189">.NET method calls.</span></span>
* <span data-ttu-id="cac74-190">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="cac74-190">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="cac74-191">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="cac74-191">For more information, see the following issues:</span></span>

* [<span data-ttu-id="cac74-192">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="cac74-192">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="cac74-193">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="cac74-193">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="cac74-194">对 JS 互操作调用的大小限制</span><span class="sxs-lookup"><span data-stu-id="cac74-194">Size limits on JS interop calls</span></span>

<span data-ttu-id="cac74-195">在 Blazor WebAssembly 中，框架对 JS 互操作输入和输出的大小不施加限制。</span><span class="sxs-lookup"><span data-stu-id="cac74-195">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="cac74-196">在 Blazor Server 中，JS 互操作调用的大小受中心方法允许的最大传入 SignalR 消息大小限制，该限制由 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>（默认值：32 KB）实施。</span><span class="sxs-lookup"><span data-stu-id="cac74-196">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="cac74-197">JS 到 .NET 的 SignalR 消息大于 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 时会引发错误。</span><span class="sxs-lookup"><span data-stu-id="cac74-197">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="cac74-198">框架对从中心到客户端的 SignalR 消息大小不施加限制。</span><span class="sxs-lookup"><span data-stu-id="cac74-198">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="cac74-199">如果未将 SignalR 日志记录设置为[调试](xref:Microsoft.Extensions.Logging.LogLevel)或[跟踪](xref:Microsoft.Extensions.Logging.LogLevel)，则消息大小错误仅显示在浏览器的开发人员工具控制台中：</span><span class="sxs-lookup"><span data-stu-id="cac74-199">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="cac74-200">错误：连接断开，出现错误“错误:服务器关闭时返回错误:连接因错误而关闭。”</span><span class="sxs-lookup"><span data-stu-id="cac74-200">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="cac74-201">将 [SignalR 服务器端日志记录](xref:signalr/diagnostics#server-side-logging)设置为[调试](xref:Microsoft.Extensions.Logging.LogLevel)或[跟踪](xref:Microsoft.Extensions.Logging.LogLevel)时，服务器端日志记录会针对消息大小错误显示 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="cac74-201">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="cac74-202">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="cac74-202">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="cac74-203">System.IO.InvalidDataException:超出了最大消息大小 32768B。</span><span class="sxs-lookup"><span data-stu-id="cac74-203">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="cac74-204">消息大小可以在 AddHubOptions 中配置。</span><span class="sxs-lookup"><span data-stu-id="cac74-204">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="cac74-205">通过在 `Startup.ConfigureServices` 中设置 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 来提高限制。</span><span class="sxs-lookup"><span data-stu-id="cac74-205">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cac74-206">以下示例将最大接收消息大小设置为 64 KB (64 \* 1024)：</span><span class="sxs-lookup"><span data-stu-id="cac74-206">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="cac74-207">提高 SignalR 传入消息大小上限的代价是需要使用更多的服务器资源，这将使服务器面临来自恶意用户的更大风险。</span><span class="sxs-lookup"><span data-stu-id="cac74-207">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="cac74-208">此外，如果将大量内容作为字符串或字节数组读入内存中，还会导致垃圾回收器的分配工作状况不佳，从而导致额外的性能损失。</span><span class="sxs-lookup"><span data-stu-id="cac74-208">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="cac74-209">读取大型有效负载的一种方法是以较小区块发送内容，并将有效负载作为 <xref:System.IO.Stream> 处理。</span><span class="sxs-lookup"><span data-stu-id="cac74-209">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cac74-210">可以在读取大型 JSON 有效负载或者数据在 JavaScript 中以原始字节形式提供时使用此方法。</span><span class="sxs-lookup"><span data-stu-id="cac74-210">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="cac74-211">有关演示如何使用类似于 `InputFile` 组件的方法在 Blazor Server 中发送大型二进制有效负载的示例，请参阅[二进制文件提交示例应用](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit)。</span><span class="sxs-lookup"><span data-stu-id="cac74-211">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="cac74-212">开发在 JavaScript 和 Blazor 之间传输大量数据的代码时，请考虑以下指南：</span><span class="sxs-lookup"><span data-stu-id="cac74-212">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="cac74-213">将数据切成小块，然后按顺序发送数据段，直到服务器收到所有数据。</span><span class="sxs-lookup"><span data-stu-id="cac74-213">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="cac74-214">不要在 JavaScript 和 C# 代码中分配大型对象。</span><span class="sxs-lookup"><span data-stu-id="cac74-214">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="cac74-215">发送或接收数据时，请勿长时间阻止主 UI 线程。</span><span class="sxs-lookup"><span data-stu-id="cac74-215">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="cac74-216">在进程完成或取消时释放消耗的所有内存。</span><span class="sxs-lookup"><span data-stu-id="cac74-216">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="cac74-217">为了安全起见，请强制执行以下附加要求：</span><span class="sxs-lookup"><span data-stu-id="cac74-217">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="cac74-218">声明可以传递的最大文件或数据大小。</span><span class="sxs-lookup"><span data-stu-id="cac74-218">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="cac74-219">声明从客户端到服务器的最低上传速率。</span><span class="sxs-lookup"><span data-stu-id="cac74-219">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="cac74-220">在服务器收到数据后，数据可以：</span><span class="sxs-lookup"><span data-stu-id="cac74-220">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="cac74-221">暂时存储在内存缓冲区中，直到收集完所有数据段。</span><span class="sxs-lookup"><span data-stu-id="cac74-221">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="cac74-222">立即使用。</span><span class="sxs-lookup"><span data-stu-id="cac74-222">Consumed immediately.</span></span> <span data-ttu-id="cac74-223">例如，在收到每个数据段时，数据可以立即存储到数据库中或写入磁盘。</span><span class="sxs-lookup"><span data-stu-id="cac74-223">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="cac74-224">JS 模块</span><span class="sxs-lookup"><span data-stu-id="cac74-224">JS modules</span></span>

<span data-ttu-id="cac74-225">对于 JS 隔离，JS 互操作适用于浏览器针对 [EcmaScript 模块 (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)) 的默认支持。</span><span class="sxs-lookup"><span data-stu-id="cac74-225">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cac74-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="cac74-226">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="cac74-227">`InteropComponent.razor` 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="cac74-227">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
