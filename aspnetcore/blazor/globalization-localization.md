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
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="c3242-103">ASP.NET Core Blazor 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="c3242-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="c3242-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c3242-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c3242-105">可以让多个区域性和语言的用户访问 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="c3242-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="c3242-106">提供以下 .NET 全球化和本地化方案：</span><span class="sxs-lookup"><span data-stu-id="c3242-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="c3242-107">.网络资源系统</span><span class="sxs-lookup"><span data-stu-id="c3242-107">.NET's resources system</span></span>
* <span data-ttu-id="c3242-108">区域性特定的数字和日期格式设置</span><span class="sxs-lookup"><span data-stu-id="c3242-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="c3242-109">目前支持有限的一组 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="c3242-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="c3242-110">Blazor 应用*支持*`IStringLocalizer<>`。</span><span class="sxs-lookup"><span data-stu-id="c3242-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="c3242-111">`IHtmlLocalizer<>`、`IViewLocalizer<>`和数据批注本地化 ASP.NET Core MVC 方案，但在 Blazor 应用程序中**不受支持**。</span><span class="sxs-lookup"><span data-stu-id="c3242-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="c3242-112">有关详细信息，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="c3242-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="c3242-113">全球化</span><span class="sxs-lookup"><span data-stu-id="c3242-113">Globalization</span></span>

Blazor<span data-ttu-id="c3242-114">的 `@bind` 功能根据用户的当前区域性来执行格式设置并分析显示的值。</span><span class="sxs-lookup"><span data-stu-id="c3242-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="c3242-115">可以从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="c3242-116">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="c3242-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="c3242-117">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="c3242-117">The preceding field types:</span></span>

* <span data-ttu-id="c3242-118">使用其基于浏览器的适当格式规则显示。</span><span class="sxs-lookup"><span data-stu-id="c3242-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="c3242-119">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="c3242-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="c3242-120">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="c3242-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="c3242-121">以下字段类型具有特定的格式要求，当前不受 Blazor 支持，因为所有主要浏览器不支持它们：</span><span class="sxs-lookup"><span data-stu-id="c3242-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="c3242-122">`@bind` 支持 `@bind:culture` 参数，以提供用于分析值并设置其格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="c3242-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="c3242-123">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="c3242-124">`date` 和 `number` 提供提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="c3242-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="c3242-125">本地化</span><span class="sxs-lookup"><span data-stu-id="c3242-125">Localization</span></span>

<span data-ttu-id="c3242-126">使用[本地化中间件](xref:fundamentals/localization#localization-middleware)本地化 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="c3242-126">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="c3242-127">中间件为从应用程序请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="c3242-128">可以使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="c3242-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="c3242-129">Cookie</span><span class="sxs-lookup"><span data-stu-id="c3242-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="c3242-130">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="c3242-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="c3242-131">有关更多信息和示例，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="c3242-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="c3242-132">为国际化配置链接器（Blazor WebAssembly）</span><span class="sxs-lookup"><span data-stu-id="c3242-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="c3242-133">默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="c3242-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="c3242-134">有关控制链接器行为的详细信息和指南，请参阅 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="c3242-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="c3242-135">Cookie</span><span class="sxs-lookup"><span data-stu-id="c3242-135">Cookies</span></span>

<span data-ttu-id="c3242-136">本地化区域性 cookie 可以保存用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="c3242-137">该 cookie 是由应用程序的主机页（*Pages/host. .cs*）的 `OnGet` 方法创建的。</span><span class="sxs-lookup"><span data-stu-id="c3242-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="c3242-138">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="c3242-139">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="c3242-140">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="c3242-141">因此，建议使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="c3242-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="c3242-142">如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="c3242-143">如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="c3242-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="c3242-144">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="c3242-145">在 Blazor Server 应用中创建包含以下内容的*页面/主机 .cs*文件：</span><span class="sxs-lookup"><span data-stu-id="c3242-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="c3242-146">本地化由应用按以下事件顺序进行处理：</span><span class="sxs-lookup"><span data-stu-id="c3242-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="c3242-147">浏览器将初始 HTTP 请求发送到应用程序。</span><span class="sxs-lookup"><span data-stu-id="c3242-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="c3242-148">区域性由本地化中间件分配。</span><span class="sxs-lookup"><span data-stu-id="c3242-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="c3242-149">*_Host*中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="c3242-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="c3242-150">浏览器将打开 WebSocket 连接以创建交互 Blazor 服务器会话。</span><span class="sxs-lookup"><span data-stu-id="c3242-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="c3242-151">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="c3242-152">Blazor Server 会话以正确的区域性开头。</span><span class="sxs-lookup"><span data-stu-id="c3242-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="c3242-153">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="c3242-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="c3242-154">为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。</span><span class="sxs-lookup"><span data-stu-id="c3242-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="c3242-155">此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况，&mdash;用户重定向到登录页，然后重定向回原始资源。</span><span class="sxs-lookup"><span data-stu-id="c3242-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="c3242-156">应用程序通过重定向到控制器来持久保存用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="c3242-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="c3242-157">控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="c3242-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="c3242-158">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="c3242-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="c3242-159">使用 `LocalRedirect` 操作结果可防止开放重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="c3242-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="c3242-160">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="c3242-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="c3242-161">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="c3242-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c3242-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="c3242-162">Additional resources</span></span>

* <xref:fundamentals/localization>
