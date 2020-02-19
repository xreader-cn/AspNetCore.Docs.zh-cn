---
title: ASP.NET Core Blazor 全球化和本地化
author: guardrex
description: 了解如何使用户能够以多种文化和语言访问 Razor 组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453186"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor 全球化和本地化

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

可以让多个区域性和语言的用户访问 Razor 组件。 提供以下 .NET 全球化和本地化方案：

* .网络资源系统
* 区域性特定的数字和日期格式设置

目前支持有限的一组 ASP.NET Core 本地化方案：

* Blazor 应用*支持*`IStringLocalizer<>`。
* `IHtmlLocalizer<>`、`IViewLocalizer<>`和数据批注本地化 ASP.NET Core MVC 方案，但在 Blazor 应用程序中**不受支持**。

有关详细信息，请参阅 <xref:fundamentals/localization>。

## <a name="globalization"></a>全球化

Blazor的 `@bind` 功能根据用户的当前区域性来执行格式设置并分析显示的值。

可以从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：

* `date`
* `number`

上述字段类型：

* 使用其基于浏览器的适当格式规则显示。
* 不能包含自由格式的文本。
* 基于浏览器的实现提供用户交互特性。

以下字段类型具有特定的格式要求，当前不受 Blazor 支持，因为所有主要浏览器不支持它们：

* `datetime-local`
* `month`
* `week`

`@bind` 支持 `@bind:culture` 参数，以提供用于分析值并设置其格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。 使用 `date` 和 `number` 字段类型时，不建议指定区域性。 `date` 和 `number` 提供提供所需区域性的内置 Blazor 支持。

## <a name="localization"></a>本地化

使用[本地化中间件](xref:fundamentals/localization#localization-middleware)本地化 Blazor Server 应用。 中间件为从应用程序请求资源的用户选择相应的区域性。

可以使用以下方法之一设置区域性：

* [Cookie](#cookies)
* [提供用于选择区域性的 UI](#provide-ui-to-choose-the-culture)

有关更多信息和示例，请参阅 <xref:fundamentals/localization>。

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>为国际化配置链接器（Blazor WebAssembly）

默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。 有关控制链接器行为的详细信息和指南，请参阅 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>。

### <a name="cookies"></a>Cookie

本地化区域性 cookie 可以保存用户的区域性。 该 cookie 是由应用程序的主机页（*Pages/host. .cs*）的 `OnGet` 方法创建的。 本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。 

使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。 如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。 因此，建议使用本地化区域性 cookie。

如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。 如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。

下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。 在 Blazor Server 应用中创建包含以下内容的*页面/主机 .cs*文件：

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

本地化由应用按以下事件顺序进行处理：

1. 浏览器将初始 HTTP 请求发送到应用程序。
1. 区域性由本地化中间件分配。
1. *_Host*中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。
1. 浏览器将打开 WebSocket 连接以创建交互 Blazor 服务器会话。
1. 本地化中间件读取 cookie 并分配区域性。
1. Blazor Server 会话以正确的区域性开头。

### <a name="provide-ui-to-choose-the-culture"></a>提供用于选择区域性的 UI

为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。 此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况，&mdash;用户重定向到登录页，然后重定向回原始资源。 

应用程序通过重定向到控制器来持久保存用户的所选区域性。 控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。

在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> 使用 `LocalRedirect` 操作结果可防止开放重定向攻击。 有关详细信息，请参阅 <xref:security/preventing-open-redirects>。

以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/localization>
