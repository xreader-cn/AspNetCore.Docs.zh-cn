---
title: ASP.NET Core Blazor 事件处理
author: guardrex
description: 了解 Blazor 的事件处理场景，包括事件参数类型、事件回调和管理默认浏览器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648330"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="ae07e-103">ASP.NET Core Blazor 事件处理</span><span class="sxs-lookup"><span data-stu-id="ae07e-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="ae07e-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ae07e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ae07e-105">Razor 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="ae07e-105">Razor components provide event handling features.</span></span> <span data-ttu-id="ae07e-106">对于具有委托类型值且名为 `on{EVENT}`（例如 `onclick` 和 `onsubmit`）的 HTML 元素属性，Razor 组件将该属性的值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="ae07e-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="ae07e-107">属性名始终的格式始终为 [`@on{EVENT}`](xref:mvc/views/razor#onevent)。</span><span class="sxs-lookup"><span data-stu-id="ae07e-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="ae07e-108">在 UI 中选择该按钮时，以下代码调用 `UpdateHeading` 方法：</span><span class="sxs-lookup"><span data-stu-id="ae07e-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="ae07e-109">UI 中的该复选框更改时，以下代码调用 `CheckChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="ae07e-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="ae07e-110">事件处理程序也可以是异步处理程序，并返回 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="ae07e-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="ae07e-111">无需手动调用 [StateHasChanged](xref:blazor/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="ae07e-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="ae07e-112">异常发生时，它们将被记录下来。</span><span class="sxs-lookup"><span data-stu-id="ae07e-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="ae07e-113">在下面的示例中，选择该按钮时，异步调用 `UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="ae07e-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="ae07e-114">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="ae07e-114">Event argument types</span></span>

<span data-ttu-id="ae07e-115">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="ae07e-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="ae07e-116">仅当方法中使用了事件类型时，才需要在方法调用中指定该事件类型。</span><span class="sxs-lookup"><span data-stu-id="ae07e-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="ae07e-117">支持的 `EventArgs` 显示在下表中。</span><span class="sxs-lookup"><span data-stu-id="ae07e-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="ae07e-118">事件</span><span class="sxs-lookup"><span data-stu-id="ae07e-118">Event</span></span>            | <span data-ttu-id="ae07e-119">类</span><span class="sxs-lookup"><span data-stu-id="ae07e-119">Class</span></span>                | <span data-ttu-id="ae07e-120">DOM 事件和说明</span><span class="sxs-lookup"><span data-stu-id="ae07e-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="ae07e-121">剪贴板</span><span class="sxs-lookup"><span data-stu-id="ae07e-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="ae07e-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="ae07e-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="ae07e-123">拖动</span><span class="sxs-lookup"><span data-stu-id="ae07e-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="ae07e-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="ae07e-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="ae07e-125">`DataTransfer` 和 `DataTransferItem` 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="ae07e-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="ae07e-126">错误</span><span class="sxs-lookup"><span data-stu-id="ae07e-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="ae07e-127">事件</span><span class="sxs-lookup"><span data-stu-id="ae07e-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="ae07e-128">*常规*</span><span class="sxs-lookup"><span data-stu-id="ae07e-128">*General*</span></span><br><span data-ttu-id="ae07e-129">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="ae07e-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="ae07e-130">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="ae07e-130">*Clipboard*</span></span><br><span data-ttu-id="ae07e-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="ae07e-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="ae07e-132">*输入*</span><span class="sxs-lookup"><span data-stu-id="ae07e-132">*Input*</span></span><br><span data-ttu-id="ae07e-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="ae07e-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="ae07e-134">*介质*</span><span class="sxs-lookup"><span data-stu-id="ae07e-134">*Media*</span></span><br><span data-ttu-id="ae07e-135">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="ae07e-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="ae07e-136">焦点</span><span class="sxs-lookup"><span data-stu-id="ae07e-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="ae07e-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="ae07e-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="ae07e-138">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="ae07e-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="ae07e-139">输入</span><span class="sxs-lookup"><span data-stu-id="ae07e-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="ae07e-140">`onchange`，`oninput`</span><span class="sxs-lookup"><span data-stu-id="ae07e-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="ae07e-141">键盘</span><span class="sxs-lookup"><span data-stu-id="ae07e-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="ae07e-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="ae07e-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="ae07e-143">鼠标</span><span class="sxs-lookup"><span data-stu-id="ae07e-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="ae07e-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="ae07e-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="ae07e-145">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="ae07e-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="ae07e-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="ae07e-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="ae07e-147">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="ae07e-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="ae07e-148">`onwheel`，`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="ae07e-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="ae07e-149">进度</span><span class="sxs-lookup"><span data-stu-id="ae07e-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="ae07e-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="ae07e-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="ae07e-151">触控</span><span class="sxs-lookup"><span data-stu-id="ae07e-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="ae07e-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="ae07e-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="ae07e-153">`TouchPoint` 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="ae07e-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="ae07e-154">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="ae07e-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="ae07e-155">[ASP.NET Core 引用源 (dotnet/aspnetcore release/3.1 branch) 中的 EventArgs 类](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="ae07e-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="ae07e-156">[MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 包括有关支持每个 DOM 事件的 HTML 元素的信息。</span><span class="sxs-lookup"><span data-stu-id="ae07e-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="ae07e-157">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="ae07e-157">Lambda expressions</span></span>

<span data-ttu-id="ae07e-158">还可以使用 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：</span><span class="sxs-lookup"><span data-stu-id="ae07e-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="ae07e-159">关闭附加值通常很方便，例如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="ae07e-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="ae07e-160">下面的示例创建了三个按钮。在 UI 中选中这些按钮时，每个按钮都调用 `UpdateHeading`传递事件参数 (`MouseEventArgs`) 和其按钮编号 (`buttonNumber`)：</span><span class="sxs-lookup"><span data-stu-id="ae07e-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="ae07e-161">不要在 Lambda 表达式中直接使用 `for` 循环中的循环变量 (`i`)  。</span><span class="sxs-lookup"><span data-stu-id="ae07e-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="ae07e-162">否则，所有 Lambda 表达式将使用相同的变量，导致所有 Lambda 中的 `i` 值相同。</span><span class="sxs-lookup"><span data-stu-id="ae07e-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="ae07e-163">始终在本地变量中捕获其值（在前面的示例中为 `buttonNumber`），然后使用它。</span><span class="sxs-lookup"><span data-stu-id="ae07e-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="ae07e-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="ae07e-164">EventCallback</span></span>

<span data-ttu-id="ae07e-165">嵌套组件的一个常见场景：希望在子组件事件发生时运行父组件的方法 &mdash; 例如当子组件中发生 `onclick` 事件时。</span><span class="sxs-lookup"><span data-stu-id="ae07e-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="ae07e-166">若要跨组件公开事件，请使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="ae07e-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="ae07e-167">父组件可向子组件的 `EventCallback` 分配回调方法。</span><span class="sxs-lookup"><span data-stu-id="ae07e-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="ae07e-168">示例应用 (Components/ChildComponent.razor) 中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 `EventCallback` 委托  。</span><span class="sxs-lookup"><span data-stu-id="ae07e-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="ae07e-169">`EventCallback` 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="ae07e-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="ae07e-170">`ParentComponent` 将子级的 `EventCallback<T>` (`OnClickCallback`) 设置为其 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="ae07e-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="ae07e-171">Pages/ParentComponent  ：</span><span class="sxs-lookup"><span data-stu-id="ae07e-171">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="ae07e-172">在 `ChildComponent` 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="ae07e-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="ae07e-173">调用 `ParentComponent` 的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="ae07e-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="ae07e-174">`_messageText` 更新并显示在 `ParentComponent` 中。</span><span class="sxs-lookup"><span data-stu-id="ae07e-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="ae07e-175">回调方法 (`ShowMessage`) 中不需要对 [StateHasChanged](xref:blazor/lifecycle#state-changes) 的调用。</span><span class="sxs-lookup"><span data-stu-id="ae07e-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="ae07e-176">自动调用 `StateHasChanged` 以重新呈现 `ParentComponent`，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。</span><span class="sxs-lookup"><span data-stu-id="ae07e-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="ae07e-177">`EventCallback` 和 `EventCallback<T>` 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="ae07e-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="ae07e-178">`EventCallback<T>` 为强类型，需要特定的参数类型。</span><span class="sxs-lookup"><span data-stu-id="ae07e-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="ae07e-179">`EventCallback` 为弱类型，允许任何参数类型。</span><span class="sxs-lookup"><span data-stu-id="ae07e-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="ae07e-180">使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>` 并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="ae07e-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="ae07e-181">使用 `EventCallback` 和 `EventCallback<T>` 处理事件和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="ae07e-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="ae07e-182">优先使用强类型 `EventCallback<T>` 而非 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="ae07e-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="ae07e-183">`EventCallback<T>` 向用户提供更好的组件错误反馈。</span><span class="sxs-lookup"><span data-stu-id="ae07e-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="ae07e-184">与其他 UI 事件处理程序类似，指定事件参数是可选操作。</span><span class="sxs-lookup"><span data-stu-id="ae07e-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="ae07e-185">当没有值传递给回调时，使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="ae07e-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="ae07e-186">阻止默认操作</span><span class="sxs-lookup"><span data-stu-id="ae07e-186">Prevent default actions</span></span>

<span data-ttu-id="ae07e-187">使用 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令属性可阻止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="ae07e-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="ae07e-188">在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。</span><span class="sxs-lookup"><span data-stu-id="ae07e-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="ae07e-189">在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令属性来阻止默认行为。</span><span class="sxs-lookup"><span data-stu-id="ae07e-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="ae07e-190">计数器递增，且 + 键不会捕获到 `<input>` 元素的值中  ：</span><span class="sxs-lookup"><span data-stu-id="ae07e-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="ae07e-191">指定没有值的 `@on{EVENT}:preventDefault` 属性等同于 `@on{EVENT}:preventDefault="true"`。</span><span class="sxs-lookup"><span data-stu-id="ae07e-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="ae07e-192">属性的值也可以是表达式。</span><span class="sxs-lookup"><span data-stu-id="ae07e-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="ae07e-193">在下面的示例中，`_shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：</span><span class="sxs-lookup"><span data-stu-id="ae07e-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="ae07e-194">不需要事件处理程序来阻止默认操作。</span><span class="sxs-lookup"><span data-stu-id="ae07e-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="ae07e-195">事件处理程序和阻止默认操作场景可以独立使用。</span><span class="sxs-lookup"><span data-stu-id="ae07e-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="ae07e-196">停止事件传播</span><span class="sxs-lookup"><span data-stu-id="ae07e-196">Stop event propagation</span></span>

<span data-ttu-id="ae07e-197">使用 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令属性来停止事件传播。</span><span class="sxs-lookup"><span data-stu-id="ae07e-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="ae07e-198">在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`：</span><span class="sxs-lookup"><span data-stu-id="ae07e-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
