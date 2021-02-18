---
title: ASP.NET Core Blazor 事件处理
author: guardrex
description: 了解 Blazor 的事件处理特性，包括事件参数类型、事件回调和管理默认浏览器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: f6a93eb9d95182d29a60cc1a5c48122b9166aa84
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280147"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="37ada-103">ASP.NET Core Blazor 事件处理</span><span class="sxs-lookup"><span data-stu-id="37ada-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="37ada-104">Razor 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="37ada-104">Razor components provide event handling features.</span></span> <span data-ttu-id="37ada-105">对于具有委托类型值的名为 [`@on{EVENT}`](xref:mvc/views/razor#onevent)（例如 `@onclick`）的 HTML 元素属性，Razor 组件将此属性的值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="37ada-105">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="37ada-106">在 UI 中选择该按钮时，以下代码调用 `UpdateHeading` 方法：</span><span class="sxs-lookup"><span data-stu-id="37ada-106">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="37ada-107">UI 中的该复选框更改时，以下代码调用 `CheckChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="37ada-107">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="37ada-108">事件处理程序也可以是异步处理程序，并返回 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="37ada-108">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="37ada-109">无需手动调用 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="37ada-109">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="37ada-110">异常发生时，它们将被记录下来。</span><span class="sxs-lookup"><span data-stu-id="37ada-110">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="37ada-111">在下面的示例中，选择该按钮时，异步调用 `UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="37ada-111">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="37ada-112">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="37ada-112">Event argument types</span></span>

<span data-ttu-id="37ada-113">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="37ada-113">For some events, event argument types are permitted.</span></span> <span data-ttu-id="37ada-114">在事件方法定义中指定事件参数是可选操作，只有当方法中使用了事件类型时才是必需的。</span><span class="sxs-lookup"><span data-stu-id="37ada-114">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="37ada-115">在下面的示例中，`ShowMessage` 方法中使用 `MouseEventArgs` 事件参数来设置消息文本：</span><span class="sxs-lookup"><span data-stu-id="37ada-115">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="37ada-116">支持的 <xref:System.EventArgs> 显示在下表中。</span><span class="sxs-lookup"><span data-stu-id="37ada-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="37ada-117">事件</span><span class="sxs-lookup"><span data-stu-id="37ada-117">Event</span></span>            | <span data-ttu-id="37ada-118">类</span><span class="sxs-lookup"><span data-stu-id="37ada-118">Class</span></span>  | <span data-ttu-id="37ada-119">DOM 事件和说明</span><span class="sxs-lookup"><span data-stu-id="37ada-119">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="37ada-120">剪贴板</span><span class="sxs-lookup"><span data-stu-id="37ada-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="37ada-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="37ada-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="37ada-122">拖动</span><span class="sxs-lookup"><span data-stu-id="37ada-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="37ada-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="37ada-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="37ada-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="37ada-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="37ada-125">使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。</span><span class="sxs-lookup"><span data-stu-id="37ada-125">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="37ada-126">错误</span><span class="sxs-lookup"><span data-stu-id="37ada-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="37ada-127">事件</span><span class="sxs-lookup"><span data-stu-id="37ada-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="37ada-128">*常规*</span><span class="sxs-lookup"><span data-stu-id="37ada-128">*General*</span></span><br><span data-ttu-id="37ada-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="37ada-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="37ada-130">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="37ada-130">*Clipboard*</span></span><br><span data-ttu-id="37ada-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="37ada-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="37ada-132">*输入*</span><span class="sxs-lookup"><span data-stu-id="37ada-132">*Input*</span></span><br><span data-ttu-id="37ada-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="37ada-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="37ada-134">*介质*</span><span class="sxs-lookup"><span data-stu-id="37ada-134">*Media*</span></span><br><span data-ttu-id="37ada-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="37ada-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="37ada-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="37ada-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="37ada-137">焦点</span><span class="sxs-lookup"><span data-stu-id="37ada-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="37ada-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="37ada-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="37ada-139">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="37ada-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="37ada-140">输入</span><span class="sxs-lookup"><span data-stu-id="37ada-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="37ada-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="37ada-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="37ada-142">键盘</span><span class="sxs-lookup"><span data-stu-id="37ada-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="37ada-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="37ada-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="37ada-144">鼠标</span><span class="sxs-lookup"><span data-stu-id="37ada-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="37ada-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="37ada-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="37ada-146">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="37ada-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="37ada-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="37ada-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="37ada-148">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="37ada-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="37ada-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="37ada-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="37ada-150">进度</span><span class="sxs-lookup"><span data-stu-id="37ada-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="37ada-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="37ada-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="37ada-152">触控</span><span class="sxs-lookup"><span data-stu-id="37ada-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="37ada-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="37ada-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="37ada-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="37ada-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="37ada-155">事件</span><span class="sxs-lookup"><span data-stu-id="37ada-155">Event</span></span>            | <span data-ttu-id="37ada-156">类</span><span class="sxs-lookup"><span data-stu-id="37ada-156">Class</span></span> | <span data-ttu-id="37ada-157">DOM 事件和说明</span><span class="sxs-lookup"><span data-stu-id="37ada-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="37ada-158">剪贴板</span><span class="sxs-lookup"><span data-stu-id="37ada-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="37ada-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="37ada-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="37ada-160">拖动</span><span class="sxs-lookup"><span data-stu-id="37ada-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="37ada-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="37ada-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="37ada-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="37ada-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="37ada-163">使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。</span><span class="sxs-lookup"><span data-stu-id="37ada-163">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="37ada-164">错误</span><span class="sxs-lookup"><span data-stu-id="37ada-164">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="37ada-165">事件</span><span class="sxs-lookup"><span data-stu-id="37ada-165">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="37ada-166">*常规*</span><span class="sxs-lookup"><span data-stu-id="37ada-166">*General*</span></span><br><span data-ttu-id="37ada-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="37ada-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="37ada-168">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="37ada-168">*Clipboard*</span></span><br><span data-ttu-id="37ada-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="37ada-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="37ada-170">*输入*</span><span class="sxs-lookup"><span data-stu-id="37ada-170">*Input*</span></span><br><span data-ttu-id="37ada-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="37ada-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="37ada-172">*介质*</span><span class="sxs-lookup"><span data-stu-id="37ada-172">*Media*</span></span><br><span data-ttu-id="37ada-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="37ada-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="37ada-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="37ada-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="37ada-175">焦点</span><span class="sxs-lookup"><span data-stu-id="37ada-175">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="37ada-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="37ada-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="37ada-177">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="37ada-177">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="37ada-178">输入</span><span class="sxs-lookup"><span data-stu-id="37ada-178">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="37ada-179">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="37ada-179">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="37ada-180">键盘</span><span class="sxs-lookup"><span data-stu-id="37ada-180">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="37ada-181">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="37ada-181">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="37ada-182">鼠标</span><span class="sxs-lookup"><span data-stu-id="37ada-182">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="37ada-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="37ada-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="37ada-184">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="37ada-184">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="37ada-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="37ada-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="37ada-186">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="37ada-186">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="37ada-187">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="37ada-187">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="37ada-188">进度</span><span class="sxs-lookup"><span data-stu-id="37ada-188">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="37ada-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="37ada-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="37ada-190">触控</span><span class="sxs-lookup"><span data-stu-id="37ada-190">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="37ada-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="37ada-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="37ada-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="37ada-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="37ada-193">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="37ada-193">For more information, see the following resources:</span></span>

* <span data-ttu-id="37ada-194">[ASP.NET Core 引用源（dotnet/aspnetcore `master` 分支）中的 `EventArgs` 类](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="37ada-194">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="37ada-195">`master` 分支表示正在针对下一个 ASP.NET Core 版本开发的 API。</span><span class="sxs-lookup"><span data-stu-id="37ada-195">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="37ada-196">对于当前版本，请选择适当的 GitHub 存储库分支（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="37ada-196">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="37ada-197">[MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含关于哪些 HTML 元素支持每个 DOM 事件的信息。</span><span class="sxs-lookup"><span data-stu-id="37ada-197">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="37ada-198">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="37ada-198">Lambda expressions</span></span>

<span data-ttu-id="37ada-199">还可以使用 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：</span><span class="sxs-lookup"><span data-stu-id="37ada-199">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="37ada-200">关闭附加值通常很方便，例如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="37ada-200">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="37ada-201">下面的示例创建了三个按钮。在 UI 中选中这些按钮时，每个按钮都调用 `UpdateHeading`传递事件参数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) 和其按钮编号 (`buttonNumber`)：</span><span class="sxs-lookup"><span data-stu-id="37ada-201">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="37ada-202">不要直接在 Lambda 表达式中使用循环变量，如前面的 `for` 循环示例中的 `i`。</span><span class="sxs-lookup"><span data-stu-id="37ada-202">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="37ada-203">否则，所有 Lambda 表达式将使用相同的变量，这将导致在所有 Lambda 中使用相同的值。</span><span class="sxs-lookup"><span data-stu-id="37ada-203">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="37ada-204">始终在局部变量中捕获该变量的值，然后使用该值。</span><span class="sxs-lookup"><span data-stu-id="37ada-204">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="37ada-205">在前面的示例中，循环变量 `i` 分配给 `buttonNumber`。</span><span class="sxs-lookup"><span data-stu-id="37ada-205">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="37ada-206">EventCallback</span><span class="sxs-lookup"><span data-stu-id="37ada-206">EventCallback</span></span>

<span data-ttu-id="37ada-207">嵌套组件的一个常见场景：希望在子组件事件发生时运行父组件的方法。</span><span class="sxs-lookup"><span data-stu-id="37ada-207">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="37ada-208">子组件中发生的 `onclick` 事件是一个常见用例。</span><span class="sxs-lookup"><span data-stu-id="37ada-208">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="37ada-209">若要跨组件公开事件，请使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="37ada-209">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="37ada-210">父组件可向子组件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 分配回调方法。</span><span class="sxs-lookup"><span data-stu-id="37ada-210">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="37ada-211">示例应用 (`Components/ChildComponent.razor`) 中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 委托。</span><span class="sxs-lookup"><span data-stu-id="37ada-211">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="37ada-212"><xref:Microsoft.AspNetCore.Components.EventCallback> 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="37ada-212">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="37ada-213">`ParentComponent` 将子级的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 设置为其 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="37ada-213">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="37ada-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="37ada-214">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="37ada-215">在 `ChildComponent` 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="37ada-215">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="37ada-216">调用 `ParentComponent` 的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="37ada-216">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="37ada-217">`messageText` 更新并显示在 `ParentComponent` 中。</span><span class="sxs-lookup"><span data-stu-id="37ada-217">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="37ada-218">回调方法 (`ShowMessage`) 中不需要对 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) 的调用。</span><span class="sxs-lookup"><span data-stu-id="37ada-218">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="37ada-219">自动调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以重新呈现 `ParentComponent`，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。</span><span class="sxs-lookup"><span data-stu-id="37ada-219"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="37ada-220">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="37ada-220">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="37ada-221"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="37ada-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="37ada-222"><xref:Microsoft.AspNetCore.Components.EventCallback> 是弱类型，允许将任何类型参数传入 `InvokeAsync(Object)`。</span><span class="sxs-lookup"><span data-stu-id="37ada-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="37ada-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 是强类型，需要将 `T` 参数传入可分配到 `TValue` 的 `InvokeAsync(T)` 中。</span><span class="sxs-lookup"><span data-stu-id="37ada-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="37ada-224">使用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> 调用 <xref:Microsoft.AspNetCore.Components.EventCallback> 或 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="37ada-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="37ada-225">使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 处理事件和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="37ada-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="37ada-226">优先使用强类型 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 而非 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="37ada-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="37ada-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 向用户提供更好的组件错误反馈。</span><span class="sxs-lookup"><span data-stu-id="37ada-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="37ada-228">与其他 UI 事件处理程序类似，指定事件参数是可选操作。</span><span class="sxs-lookup"><span data-stu-id="37ada-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="37ada-229">当没有值传递给回调时，使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="37ada-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="37ada-230">阻止默认操作</span><span class="sxs-lookup"><span data-stu-id="37ada-230">Prevent default actions</span></span>

<span data-ttu-id="37ada-231">使用 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令属性可阻止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="37ada-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="37ada-232">在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。</span><span class="sxs-lookup"><span data-stu-id="37ada-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="37ada-233">在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令属性来阻止默认行为。</span><span class="sxs-lookup"><span data-stu-id="37ada-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="37ada-234">计数器递增，且 + 键不会捕获到 `<input>` 元素的值中：</span><span class="sxs-lookup"><span data-stu-id="37ada-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="37ada-235">指定没有值的 `@on{EVENT}:preventDefault` 属性等同于 `@on{EVENT}:preventDefault="true"`。</span><span class="sxs-lookup"><span data-stu-id="37ada-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="37ada-236">属性的值也可以是表达式。</span><span class="sxs-lookup"><span data-stu-id="37ada-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="37ada-237">在下面的示例中，`shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：</span><span class="sxs-lookup"><span data-stu-id="37ada-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="37ada-238">停止事件传播</span><span class="sxs-lookup"><span data-stu-id="37ada-238">Stop event propagation</span></span>

<span data-ttu-id="37ada-239">使用 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令属性来停止事件传播。</span><span class="sxs-lookup"><span data-stu-id="37ada-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="37ada-240">在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`：</span><span class="sxs-lookup"><span data-stu-id="37ada-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="37ada-241">聚焦元素</span><span class="sxs-lookup"><span data-stu-id="37ada-241">Focus an element</span></span>

<span data-ttu-id="37ada-242">在[元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上调用 `FocusAsync` 来聚集代码中的元素：</span><span class="sxs-lookup"><span data-stu-id="37ada-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
