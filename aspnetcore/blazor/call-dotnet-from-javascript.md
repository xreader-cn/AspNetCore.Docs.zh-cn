---
<span data-ttu-id="c4860-101">title:“在 ASP.NET Core Blazor 中从 JavaScript 函数调用 .NET 方法”author: description:“了解如何在 Blazor 应用中通过 JavaScript 函数调用 .NET 方法。”</span><span class="sxs-lookup"><span data-stu-id="c4860-101">title: 'Call .NET methods from JavaScript functions in ASP.NET Core Blazor' author: description: 'Learn how to invoke .NET methods from JavaScript functions in Blazor apps.'</span></span>
<span data-ttu-id="c4860-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c4860-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c4860-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c4860-103">'Blazor'</span></span>
- <span data-ttu-id="c4860-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c4860-104">'Identity'</span></span>
- <span data-ttu-id="c4860-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c4860-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c4860-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c4860-106">'Razor'</span></span>
- <span data-ttu-id="c4860-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c4860-107">'SignalR' uid:</span></span> 

---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="c4860-108">从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="c4860-108">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="c4860-109">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Shashikant Rudrawadi](http://wisne.co) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4860-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4860-110">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="c4860-111">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="c4860-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="c4860-112">本文介绍如何从 JavaScript 调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-112">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="c4860-113">要详细了解如何通过 .NET 调用 JavaScript 函数，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="c4860-113">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="c4860-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c4860-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="c4860-115">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="c4860-115">Static .NET method call</span></span>

<span data-ttu-id="c4860-116">要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="c4860-116">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="c4860-117">传入要调用的静态方法的标识符、包含该函数的程序集的名称以及任意自变量。</span><span class="sxs-lookup"><span data-stu-id="c4860-117">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="c4860-118">异步版本是支持 Blazor 服务器方案的首选。</span><span class="sxs-lookup"><span data-stu-id="c4860-118">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="c4860-119">该 .NET 方法必须是公共的静态方法，并具有 `[JSInvokable]` 特性。</span><span class="sxs-lookup"><span data-stu-id="c4860-119">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="c4860-120">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-120">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="c4860-121">该示例应用包含一个 C# 方法，用于返回 `int` 数组。</span><span class="sxs-lookup"><span data-stu-id="c4860-121">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="c4860-122">`JSInvokable` 特性应用于该方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-122">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="c4860-123">Pages/JsInterop.razor：</span><span class="sxs-lookup"><span data-stu-id="c4860-123">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="c4860-124">为客户端提供的 JavaScript 会调用 C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-124">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="c4860-125">wwwroot/exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="c4860-125">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="c4860-126">如果选择了“触发 .NET 静态方法 ReturnArrayAsync”按钮，请在浏览器的 Web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="c4860-126">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="c4860-127">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="c4860-127">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="c4860-128">第四个数组值推送到 `ReturnArrayAsync` 返回的数组 (`data.push(4);`)。</span><span class="sxs-lookup"><span data-stu-id="c4860-128">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="c4860-129">默认情况下，方法标识符为方法名称，但你可使用 `JSInvokableAttribute` 构造函数指定其他标识符：</span><span class="sxs-lookup"><span data-stu-id="c4860-129">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="c4860-130">在客户端 JavaScript 文件中：</span><span class="sxs-lookup"><span data-stu-id="c4860-130">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="c4860-131">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="c4860-131">Instance method call</span></span>

<span data-ttu-id="c4860-132">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-132">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="c4860-133">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="c4860-133">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="c4860-134">按引用向 JavaScript 传递 .NET 实例：</span><span class="sxs-lookup"><span data-stu-id="c4860-134">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="c4860-135">对 `DotNetObjectReference.Create` 进行静态调用。</span><span class="sxs-lookup"><span data-stu-id="c4860-135">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="c4860-136">在 `DotNetObjectReference` 实例中包装实例，并在 `DotNetObjectReference` 实例上调用 `Create`。</span><span class="sxs-lookup"><span data-stu-id="c4860-136">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="c4860-137">处置 `DotNetObjectReference` 对象（本部分稍后会展示一个示例）。</span><span class="sxs-lookup"><span data-stu-id="c4860-137">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="c4860-138">使用 `invokeMethod` 或 `invokeMethodAsync` 函数在实例上调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-138">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="c4860-139">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为自变量传递。</span><span class="sxs-lookup"><span data-stu-id="c4860-139">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="c4860-140">示例应用会将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="c4860-140">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="c4860-141">对于示例应用展示的以下示例，请在浏览器的开发人员工具中检查浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="c4860-141">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="c4860-142">选择“触发 .NET 实例方法 HelloHelper.SayHello”按钮时，将调用 `ExampleJsInterop.CallHelloHelperSayHello`，并将名称 `Blazor` 传递到方法。</span><span class="sxs-lookup"><span data-stu-id="c4860-142">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="c4860-143">Pages/JsInterop.razor：</span><span class="sxs-lookup"><span data-stu-id="c4860-143">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="c4860-144">`CallHelloHelperSayHello` 使用 `HelloHelper` 的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="c4860-144">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="c4860-145">JsInteropClasses/ExampleJsInterop.cs：</span><span class="sxs-lookup"><span data-stu-id="c4860-145">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="c4860-146">wwwroot/exampleJsInterop.js：</span><span class="sxs-lookup"><span data-stu-id="c4860-146">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="c4860-147">该名称将传递给 `HelloHelper` 的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="c4860-147">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="c4860-148">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="c4860-148">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="c4860-149">JsInteropClasses/HelloHelper.cs：</span><span class="sxs-lookup"><span data-stu-id="c4860-149">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="c4860-150">浏览器 Web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="c4860-150">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="c4860-151">为避免内存泄露并允许对创建 `DotNetObjectReference` 的组件进行垃圾回收，请采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="c4860-151">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="c4860-152">处置类中创建了 `DotNetObjectReference` 实例的对象：</span><span class="sxs-lookup"><span data-stu-id="c4860-152">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="c4860-153">还可以在组件中实现上述 `ExampleJsInterop` 类中所示的模式：</span><span class="sxs-lookup"><span data-stu-id="c4860-153">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="c4860-154">如果组件或类不处置 `DotNetObjectReference`，请通过调用 `.dispose()` 在客户端上处置该对象：</span><span class="sxs-lookup"><span data-stu-id="c4860-154">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="c4860-155">组件实例方法调用</span><span class="sxs-lookup"><span data-stu-id="c4860-155">Component instance method call</span></span>

<span data-ttu-id="c4860-156">要调用组件的 .NET 方法，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c4860-156">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="c4860-157">使用 `invokeMethod` 或 `invokeMethodAsync` 函数对组件执行静态方法调用。</span><span class="sxs-lookup"><span data-stu-id="c4860-157">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="c4860-158">组件的静态方法将其实例方法调用包装为已调用的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c4860-158">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="c4860-159">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="c4860-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="c4860-160">Pages/JSInteropComponent.razor：</span><span class="sxs-lookup"><span data-stu-id="c4860-160">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="c4860-161">如果有多个组件，每个组件都有要调用的实例方法，请使用 helper 类来调用每个组件的实例方法（如 `Action`）。</span><span class="sxs-lookup"><span data-stu-id="c4860-161">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="c4860-162">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="c4860-162">In the following example:</span></span>

* <span data-ttu-id="c4860-163">`JSInterop` 组件包含若干 `ListItem` 组件。</span><span class="sxs-lookup"><span data-stu-id="c4860-163">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="c4860-164">每个 `ListItem` 组件都由一个消息和一个按钮组成。</span><span class="sxs-lookup"><span data-stu-id="c4860-164">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="c4860-165">选择 `ListItem` 组件按钮后，`ListItem` 的 `UpdateMessage` 方法会更改列表项文本并隐藏该按钮。</span><span class="sxs-lookup"><span data-stu-id="c4860-165">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="c4860-166">MessageUpdateInvokeHelper.cs：</span><span class="sxs-lookup"><span data-stu-id="c4860-166">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="c4860-167">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="c4860-167">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="c4860-168">Shared/ListItem.razor：</span><span class="sxs-lookup"><span data-stu-id="c4860-168">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

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
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="c4860-169">Pages/JSInterop.razor：</span><span class="sxs-lookup"><span data-stu-id="c4860-169">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="c4860-170">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="c4860-170">Avoid circular object references</span></span>

<span data-ttu-id="c4860-171">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="c4860-171">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="c4860-172">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="c4860-172">.NET method calls.</span></span>
* <span data-ttu-id="c4860-173">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="c4860-173">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="c4860-174">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="c4860-174">For more information, see the following issues:</span></span>

* [<span data-ttu-id="c4860-175">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="c4860-175">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="c4860-176">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="c4860-176">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="c4860-177">其他资源</span><span class="sxs-lookup"><span data-stu-id="c4860-177">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="c4860-178">InteropComponent.razor 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="c4860-178">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="c4860-179">[在 Blazor 服务器应用中执行大型数据传输](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="c4860-179">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
