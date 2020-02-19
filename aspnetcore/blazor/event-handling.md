---
title: ASP.NET Core Blazor 事件处理
author: guardrex
description: 了解 Blazor的事件处理方案，包括事件参数类型、事件回调和管理默认浏览器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453162"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 事件处理

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

Razor 组件提供事件处理功能。 对于名为 `on{EVENT}` 的 HTML 元素特性（例如，`onclick` 和 `onsubmit`）与委托类型的值，Razor 组件将属性值视为事件处理程序。 特性的名称始终[`@on{EVENT}`](xref:mvc/views/razor#onevent)格式。

当在 UI 中选择该按钮时，以下代码将调用 `UpdateHeading` 方法：

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

下面的代码在 UI 中的复选框发生更改时调用 `CheckChanged` 方法：

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

事件处理程序也可以是异步的，并返回 <xref:System.Threading.Tasks.Task>。 无需手动调用[StateHasChanged](xref:blazor/lifecycle#state-changes)。 异常在发生时进行记录。

在下面的示例中，当选择按钮时，将异步调用 `UpdateHeading`：

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

## <a name="event-argument-types"></a>事件参数类型

对于某些事件，允许使用事件参数类型。 仅当在方法中使用了事件类型时，才需要在方法调用中指定事件类型。

下表显示了支持的 `EventArgs`。

| 事件            | 类                | DOM 事件和说明 |
| ---------------- | -------------------- | -------------------- |
| 剪贴板        | `ClipboardEventArgs` | `oncut`、`oncopy`、`onpaste` |
| 入             | `DragEventArgs`      | `ondrag`、`ondragstart`、`ondragenter`、`ondragleave`、`ondragover`、`ondrop`、`ondragend`<br><br>`DataTransfer` 和 `DataTransferItem` 保存拖动的项数据。 |
| 错误            | `ErrorEventArgs`     | `onerror` |
| 事件            | `EventArgs`          | *常规*<br>`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`<br><br>*剪贴板*<br>`onbeforecut`、`onbeforecopy`、`onbeforepaste`<br><br>*输入*<br>`oninvalid`、`onreset`、`onselect`、`onselectionchange`、`onselectstart`、`onsubmit`<br><br>*介质*<br>`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting` |
| 聚焦            | `FocusEventArgs`     | `onfocus`、`onblur`、`onfocusin`、`onfocusout`<br><br>不包含对 `relatedTarget`的支持。 |
| 输入            | `ChangeEventArgs`    | `onchange`、`oninput` |
| 键盘         | `KeyboardEventArgs`  | `onkeydown`、`onkeypress`、`onkeyup` |
| 鼠标            | `MouseEventArgs`     | `onclick`、`oncontextmenu`、`ondblclick`、`onmousedown`、`onmouseup`、`onmouseover`、`onmousemove`、`onmouseout` |
| 鼠标指针    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 鼠标滚轮      | `WheelEventArgs`     | `onwheel`、`onmousewheel` |
| 进度         | `ProgressEventArgs`  | `onabort`、`onload`、`onloadend`、`onloadstart`、`onprogress`、`ontimeout` |
| 触控            | `TouchEventArgs`     | `ontouchstart`、`ontouchend`、`ontouchmove`、`ontouchenter`、`ontouchleave`、`ontouchcancel`<br><br>`TouchPoint` 表示触控相关设备上的单个联系点。 |

有关详细信息，请参阅以下资源：

* [ASP.NET Core 引用源中的 EventArgs 类（dotnet/aspnetcore release/3.1 分支）](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。
* [MDN web 文档： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 包括有关哪些 HTML 元素支持每个 DOM 事件的信息。

## <a name="lambda-expressions"></a>Lambda 表达式

还可以使用[Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

通常可以很方便地关闭其他值，如在循环访问一组元素时。 下面的示例创建了三个按钮，每个按钮都 `UpdateHeading` 在用户界面中选择时传递事件参数（`MouseEventArgs`）和按钮号（`buttonNumber`）：

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
> 不要直接在 lambda 表达式**中使用 `for`** 循环中的循环变量（`i`）。 否则，所有 lambda 表达式都将使用相同的变量，导致 `i`的值在所有 lambda 中都相同。 始终捕获其在本地变量中的值（在前面的示例中为`buttonNumber`），然后使用它。

## <a name="eventcallback"></a>EventCallback

使用嵌套组件的常见方案是，当发生子组件事件时，需要运行父组件的方法&mdash;例如，在子组件发生 `onclick` 事件时。 若要跨组件公开事件，请使用 `EventCallback`。 父组件可将回调方法分配给子组件的 `EventCallback`。

示例应用（*ChildComponent*）中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序，以便从示例的 `ParentComponent`接收 `EventCallback` 委托。 `EventCallback` 是使用 `MouseEventArgs`键入的，这适用于来自外围设备的 `onclick` 事件：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` 将子级的 `EventCallback<T>` （`OnClickCallback`）设置为它的 `ShowMessage` 方法。

*Pages/ParentComponent*：

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

在 `ChildComponent`中选择该按钮时：

* 调用 `ParentComponent`的 `ShowMessage` 方法。 `_messageText` 会更新并显示在 `ParentComponent`中。
* 回调的方法（`ShowMessage`）中不需要调用[StateHasChanged](xref:blazor/lifecycle#state-changes) 。 `StateHasChanged` 将自动调用以诸如此类 `ParentComponent`，就像子事件在子中执行的事件处理程序中 rerendering。

`EventCallback` 和 `EventCallback<T>` 允许异步委托。 `EventCallback<T>` 为强类型，并且需要特定的参数类型。 `EventCallback` 弱类型化，并允许任何参数类型。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>`，并等待 <xref:System.Threading.Tasks.Task>：

```csharp
await callback.InvokeAsync(arg);
```

使用 `EventCallback` 和 `EventCallback<T>` 进行事件处理和绑定组件参数。

优先使用强类型 `EventCallback<T>` `EventCallback`。 `EventCallback<T>` 向组件用户提供更好的错误反馈。 与其他 UI 事件处理程序类似，指定事件参数是可选的。 当没有值传递到回调时，使用 `EventCallback`。

## <a name="prevent-default-actions"></a>阻止默认操作

使用[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)指令特性可防止事件的默认操作。

如果在输入设备上选择了某个键，并且该元素焦点位于某个文本框上，则浏览器通常会在文本框中显示该键的字符。 在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令特性来阻止默认行为。 计数器会递增，并且不会将 **+** 键捕获到 `<input>` 元素的值中：

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

在不使用值的情况下指定 `@on{EVENT}:preventDefault` 特性等效于 `@on{EVENT}:preventDefault="true"`。

特性的值还可以是表达式。 在下面的示例中，`_shouldPreventDefault` 是设置为 `true` 或 `false`的 `bool` 字段：

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

不需要使用事件处理程序来防止默认操作。 可以单独使用事件处理程序和阻止默认操作方案。

## <a name="stop-event-propagation"></a>停止事件传播

使用[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)指令特性来停止事件传播。

在下面的示例中，选中此复选框可阻止单击第二个子 `<div>` 中的事件传播到父 `<div>`：

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
