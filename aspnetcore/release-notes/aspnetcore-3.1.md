---
title: ASP.NET Core 3.1 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 3.1 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 6a03e35495e2ae545dc0a3cdd38578b433d8df6b
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102587"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="c548e-103">ASP.NET Core 3.1 的新增功能</span><span class="sxs-lookup"><span data-stu-id="c548e-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="c548e-104">本文重点介绍 ASP.NET Core 3.1 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="c548e-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="c548e-105">Razor 组件的分部类支持</span><span class="sxs-lookup"><span data-stu-id="c548e-105">Partial class support for Razor components</span></span>

Razor<span data-ttu-id="c548e-106"> 组件现作为分部类生成。</span><span class="sxs-lookup"><span data-stu-id="c548e-106"> components are now generated as partial classes.</span></span> <span data-ttu-id="c548e-107">可使用定义为分部类的代码隐藏文件来编写 Razor 组件的代码，而不是在单个文件中定义该组件的所有代码。</span><span class="sxs-lookup"><span data-stu-id="c548e-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="c548e-108">有关详细信息，请参阅[分部类支持](xref:blazor/components/index#partial-class-support)。</span><span class="sxs-lookup"><span data-stu-id="c548e-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="c548e-109"> 组件标记帮助程序和将参数传递到顶级组件</span><span class="sxs-lookup"><span data-stu-id="c548e-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="c548e-110">在 ASP.NET Core 3.0 的 Blazor 中，使用 HTML 帮助程序 (`Html.RenderComponentAsync`) 将组件呈现到页面和视图中。</span><span class="sxs-lookup"><span data-stu-id="c548e-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="c548e-111">在 ASP.NET Core 3.1 中，使用新的组件标记帮助程序从页面或视图呈现组件：</span><span class="sxs-lookup"><span data-stu-id="c548e-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="c548e-112">HTML 帮助程序在 ASP.NET Core 3.1 仍受支持，但建议使用组件标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="c548e-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="c548e-113"> 服务器应用现可在初始呈现期间将参数传递给顶级组件。</span><span class="sxs-lookup"><span data-stu-id="c548e-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="c548e-114">之前，你只能将参数传递给具有 [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) 的顶级组件。</span><span class="sxs-lookup"><span data-stu-id="c548e-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="c548e-115">在此版本中，[RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 和 [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) 均受支持。</span><span class="sxs-lookup"><span data-stu-id="c548e-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="c548e-116">任何指定的参数值均序列化为 JSON，并包含在初始响应中。</span><span class="sxs-lookup"><span data-stu-id="c548e-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="c548e-117">例如，通过增量 (`IncrementAmount`) 预呈现一个 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="c548e-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="c548e-118">有关详细信息，请参阅[将组件集成到 Razor Pages 和 MVC 应用](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps)。</span><span class="sxs-lookup"><span data-stu-id="c548e-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="c548e-119">HTTP.sys 中对共享队列的支持</span><span class="sxs-lookup"><span data-stu-id="c548e-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="c548e-120">[HTTP.sys](xref:fundamentals/servers/httpsys) 支持创建匿名请求队列。</span><span class="sxs-lookup"><span data-stu-id="c548e-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="c548e-121">在 ASP.NET Core 3.1 中，我们添加了创建 HTTP.sys 请求队列或附加到现有 HTTP.sys 请求队列的功能。</span><span class="sxs-lookup"><span data-stu-id="c548e-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="c548e-122">通过创建名为 HTTP.sys 的请求队列或附加到现有 HTTP.sys 请求队列，可实现拥有该队列的 HTTP.sys 控制器进程独立于侦听器进程这一场景。</span><span class="sxs-lookup"><span data-stu-id="c548e-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="c548e-123">利用这种独立性，可在侦听器进程重启期间保留现有的连接和排队的请求：</span><span class="sxs-lookup"><span data-stu-id="c548e-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="c548e-124">SameSite Cookie 的中断性变更</span><span class="sxs-lookup"><span data-stu-id="c548e-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="c548e-125">SameSite Cookie 的行为已更改，可反映出即将发生的浏览器更改。</span><span class="sxs-lookup"><span data-stu-id="c548e-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="c548e-126">这可能会影响 AzureAd、OpenIdConnect 或 WsFederation 等身份验证场景。</span><span class="sxs-lookup"><span data-stu-id="c548e-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="c548e-127">有关详细信息，请参阅 <xref:security/samesite>。</span><span class="sxs-lookup"><span data-stu-id="c548e-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a><span data-ttu-id="c548e-128">在 Blazor 应用中阻止事件的默认操作</span><span class="sxs-lookup"><span data-stu-id="c548e-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="c548e-129">使用 `@on{EVENT}:preventDefault` 指令属性可阻止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="c548e-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="c548e-130">在下例中，阻止在文本框中显示键字符的默认操作：</span><span class="sxs-lookup"><span data-stu-id="c548e-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="c548e-131">有关详细信息，请参阅[阻止默认操作](xref:blazor/components/event-handling#prevent-default-actions)。</span><span class="sxs-lookup"><span data-stu-id="c548e-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-blazor-apps"></a><span data-ttu-id="c548e-132">在 Blazor 应用中停止事件传播</span><span class="sxs-lookup"><span data-stu-id="c548e-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="c548e-133">使用 `@on{EVENT}:stopPropagation` 指令属性来停止事件传播。</span><span class="sxs-lookup"><span data-stu-id="c548e-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="c548e-134">在下例中，选中复选框可阻止子 `<div>` 中的单击事件传播到父 `<div>`：</span><span class="sxs-lookup"><span data-stu-id="c548e-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="c548e-135">有关详细信息，请参阅[停止事件传播](xref:blazor/components/event-handling#stop-event-propagation)。</span><span class="sxs-lookup"><span data-stu-id="c548e-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-blazor-app-development"></a><span data-ttu-id="c548e-136">Blazor 应用开发过程中的错误详细信息</span><span class="sxs-lookup"><span data-stu-id="c548e-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="c548e-137">当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。</span><span class="sxs-lookup"><span data-stu-id="c548e-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="c548e-138">出现错误时，Blazor 应用会在屏幕底部显示一个黄色条框：</span><span class="sxs-lookup"><span data-stu-id="c548e-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="c548e-139">在开发过程中，黄色条框会将你定向到浏览器控制台，你可在其中查看异常。</span><span class="sxs-lookup"><span data-stu-id="c548e-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="c548e-140">在生产过程中，黄色条框会通知用户发生了错误，并建议刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="c548e-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="c548e-141">有关详细信息，请参阅[开发过程中的错误详细信息](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)。</span><span class="sxs-lookup"><span data-stu-id="c548e-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
