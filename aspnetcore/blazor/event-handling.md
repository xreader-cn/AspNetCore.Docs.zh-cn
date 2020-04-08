---
title: ASP.NET Core Blazor 事件处理
author: guardrex
description: 了解 Blazor 的事件处理特性，包括事件参数类型、事件回调和管理默认浏览器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511361"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 事件处理

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 组件提供事件处理功能。 对于具有委托类型值且名为 [`@on{EVENT}`](xref:mvc/views/razor#onevent)（例如 `@onclick`）的 HTML 元素特性，Razor 组件将该特性的值视为事件处理程序。

在 UI 中选择该按钮时，以下代码调用 `UpdateHeading` 方法：

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

UI 中的该复选框更改时，以下代码调用 `CheckChanged` 方法：

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

事件处理程序也可以是异步处理程序，并返回 <xref:System.Threading.Tasks.Task>。 无需手动调用 [StateHasChanged](xref:blazor/lifecycle#state-changes)。 异常发生时，它们将被记录下来。

在下面的示例中，选择该按钮时，异步调用 `UpdateHeading`：

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

对于某些事件，允许使用事件参数类型。 仅当方法中使用了事件类型时，才需要在方法调用中指定该事件类型。

支持的 `EventArgs` 显示在下表中。

| 事件            | 类                | DOM 事件和说明 |
| ---------------- | -------------------- | -------------------- |
| 剪贴板        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| 拖动             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` 和 `DataTransferItem` 保留拖动的项数据。 |
| 错误            | `ErrorEventArgs`     | `onerror` |
| 事件            | `EventArgs`          | *常规*<br>`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`<br><br>*剪贴板*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*输入*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*介质*<br>`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting` |
| 焦点            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>不包含对 `relatedTarget` 的支持。 |
| 输入            | `ChangeEventArgs`    | `onchange`，`oninput` |
| 键盘         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| 鼠标            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 鼠标指针    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 鼠标滚轮      | `WheelEventArgs`     | `onwheel`，`onmousewheel` |
| 进度         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 触控            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` 表示触控敏感型设备上的单个接触点。 |

有关更多信息，请参见以下资源：

* [ASP.NET Core 引用源 (dotnet/aspnetcore release/3.1 branch) 中的 EventArgs 类](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。
* [MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 包括有关支持每个 DOM 事件的 HTML 元素的信息。

## <a name="lambda-expressions"></a>Lambda 表达式

还可以使用 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

关闭附加值通常很方便，例如在循环访问一组元素时。 下面的示例创建了三个按钮。在 UI 中选中这些按钮时，每个按钮都调用 `UpdateHeading`传递事件参数 (`MouseEventArgs`) 和其按钮编号 (`buttonNumber`)：

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
> 不要在 Lambda 表达式中直接使用 `for` 循环中的循环变量 (`i`)  。 否则，所有 Lambda 表达式将使用相同的变量，导致所有 Lambda 中的 `i` 值相同。 始终在本地变量中捕获其值（在前面的示例中为 `buttonNumber`），然后使用它。

## <a name="eventcallback"></a>EventCallback

嵌套组件的一个常见场景：希望在子组件事件发生时运行父组件的方法 &mdash; 例如当子组件中发生 `onclick` 事件时。 若要跨组件公开事件，请使用 `EventCallback`。 父组件可向子组件的 `EventCallback` 分配回调方法。

示例应用 (Components/ChildComponent.razor) 中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 `EventCallback` 委托  。 `EventCallback` 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` 将子级的 `EventCallback<T>` (`OnClickCallback`) 设置为其 `ShowMessage` 方法。

Pages/ParentComponent  ：

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

在 `ChildComponent` 中选择该按钮时：

* 调用 `ParentComponent` 的 `ShowMessage` 方法。 `_messageText` 更新并显示在 `ParentComponent` 中。
* 回调方法 (`ShowMessage`) 中不需要对 [StateHasChanged](xref:blazor/lifecycle#state-changes) 的调用。 自动调用 `StateHasChanged` 以重新呈现 `ParentComponent`，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。

`EventCallback` 和 `EventCallback<T>` 允许异步委托。 `EventCallback<T>` 为强类型，需要特定的参数类型。 `EventCallback` 为弱类型，允许任何参数类型。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>` 并等待 <xref:System.Threading.Tasks.Task>：

```csharp
await callback.InvokeAsync(arg);
```

使用 `EventCallback` 和 `EventCallback<T>` 处理事件和绑定组件参数。

优先使用强类型 `EventCallback<T>` 而非 `EventCallback`。 `EventCallback<T>` 向用户提供更好的组件错误反馈。 与其他 UI 事件处理程序类似，指定事件参数是可选操作。 当没有值传递给回调时，使用 `EventCallback`。

## <a name="prevent-default-actions"></a>阻止默认操作

使用 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令属性可阻止事件的默认操作。

在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。 在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令属性来阻止默认行为。 计数器递增，且 + 键不会捕获到 `<input>` 元素的值中  ：

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

指定没有值的 `@on{EVENT}:preventDefault` 属性等同于 `@on{EVENT}:preventDefault="true"`。

属性的值也可以是表达式。 在下面的示例中，`_shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

不需要事件处理程序来阻止默认操作。 事件处理程序和阻止默认操作场景可以独立使用。

## <a name="stop-event-propagation"></a>停止事件传播

使用 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令属性来停止事件传播。

在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`：

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
