---
title: ASP.NET Core 3.1 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 3.1 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648768"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3.1 的新增功能

本文重点介绍 ASP.NET Core 3.1 中最重要的更改，并提供相关文档的链接。

## <a name="partial-class-support-for-razor-components"></a>Razor 组件的分部类支持

Razor 组件现作为分部类生成。 可使用定义为分部类的代码隐藏文件来编写 Razor 组件的代码，而不是在单个文件中定义该组件的所有代码。 有关详细信息，请参阅[分部类支持](xref:blazor/components#partial-class-support)。

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor 组件标记帮助程序和将参数传递到顶级组件

在 ASP.NET Core 3.0 的 Blazor 中，使用 HTML 帮助程序 (`Html.RenderComponentAsync`) 将组件呈现到页面和视图中。 在 ASP.NET Core 3.1 中，使用新的组件标记帮助程序从页面或视图呈现组件：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML 帮助程序在 ASP.NET Core 3.1 仍受支持，但建议使用组件标记帮助程序。

Blazor 服务器应用现可在初始呈现期间将参数传递给顶级组件。 之前，你只能将参数传递给具有 [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) 的顶级组件。 在此版本中，[RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 和 [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) 均受支持。 任何指定的参数值均序列化为 JSON，并包含在初始响应中。

例如，通过增量 (`Counter`) 预呈现一个 `IncrementAmount` 组件：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

有关详细信息，请参阅[将组件集成到 Razor 页面和 MVC 应用](xref:blazor/integrate-components)。

## <a name="support-for-shared-queues-in-httpsys"></a>HTTP.sys 中对共享队列的支持

[HTTP.sys](xref:fundamentals/servers/httpsys) 支持创建匿名请求队列。 在 ASP.NET Core 3.1 中，我们添加了创建 HTTP.sys 请求队列或附加到现有 HTTP.sys 请求队列的功能。 通过创建名为 HTTP.sys 的请求队列或附加到现有 HTTP.sys 请求队列，可实现拥有该队列的 HTTP.sys 控制器进程独立于侦听器进程这一场景。 利用这种独立性，可在侦听器进程重启期间保留现有的连接和排队的请求：

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>SameSite Cookie 的中断性变更

SameSite Cookie 的行为已更改，可反映出即将发生的浏览器更改。 这可能会影响 AzureAd、OpenIdConnect 或 WsFederation 等身份验证场景。 有关更多信息，请参见 <xref:security/samesite>。

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>在 Blazor 应用中阻止事件的默认操作

使用 `@on{EVENT}:preventDefault` 指令属性可阻止事件的默认操作。 在下例中，阻止在文本框中显示键字符的默认操作：

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

有关详细信息，请参阅[阻止默认操作](xref:blazor/event-handling#prevent-default-actions)。

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>在 Blazor 应用中停止事件传播

使用 `@on{EVENT}:stopPropagation` 指令属性来停止事件传播。 在下例中，选中复选框可阻止子 `<div>` 中的单击事件传播到父 `<div>`：

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

有关详细信息，请参阅[停止事件传播](xref:blazor/event-handling#stop-event-propagation)。

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Blazor 应用开发过程中的错误详细信息

当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。 出现错误时，Blazor 应用会在屏幕底部显示一个黄色条框：

* 在开发过程中，黄色条框会将你定向到浏览器控制台，你可在其中查看异常。
* 在生产过程中，黄色条框会通知用户发生了错误，并建议刷新浏览器。

有关详细信息，请参阅[开发过程中的错误详细信息](xref:blazor/handle-errors#detailed-errors-during-development)。
