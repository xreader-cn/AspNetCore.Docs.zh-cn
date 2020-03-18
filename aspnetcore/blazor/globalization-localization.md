---
title: ASP.NET Core Blazor 全球化和本地化
author: guardrex
description: 了解如何使 Razor 组件能够供位于不同区域、使用不同语言的用户使用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644892"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="85cdf-103">ASP.NET Core Blazor 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="85cdf-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="85cdf-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="85cdf-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="85cdf-105">Razor 组件可供位于不同区域、使用不同语言的用户使用。</span><span class="sxs-lookup"><span data-stu-id="85cdf-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="85cdf-106">以下 .NET 全球化和本地化方案可用：</span><span class="sxs-lookup"><span data-stu-id="85cdf-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="85cdf-107">.NET 资源系统</span><span class="sxs-lookup"><span data-stu-id="85cdf-107">.NET's resources system</span></span>
* <span data-ttu-id="85cdf-108">特定于区域性的数字和日期格式</span><span class="sxs-lookup"><span data-stu-id="85cdf-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="85cdf-109">当前支持有限的 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="85cdf-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="85cdf-110">Blazor 应用中支持 `IStringLocalizer<>`  。</span><span class="sxs-lookup"><span data-stu-id="85cdf-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="85cdf-111">`IHtmlLocalizer<>`、`IViewLocalizer<>` 和数据注释本地化是 ASP.NET Core MVC 方案，在 Blazor 应用中不受支持  。</span><span class="sxs-lookup"><span data-stu-id="85cdf-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="85cdf-112">有关详细信息，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="85cdf-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="85cdf-113">全球化</span><span class="sxs-lookup"><span data-stu-id="85cdf-113">Globalization</span></span>

Blazor<span data-ttu-id="85cdf-114"> 的 `@bind` 功能基于用户的当前区域性执行格式并分析值以进行显示。</span><span class="sxs-lookup"><span data-stu-id="85cdf-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="85cdf-115">可从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="85cdf-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 用于以下字段类型 (`<input type="{TYPE}" />`)：</span><span class="sxs-lookup"><span data-stu-id="85cdf-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="85cdf-117">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="85cdf-117">The preceding field types:</span></span>

* <span data-ttu-id="85cdf-118">使用其基于浏览器的相应格式规则进行显示。</span><span class="sxs-lookup"><span data-stu-id="85cdf-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="85cdf-119">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="85cdf-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="85cdf-120">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="85cdf-121">以下字段类型具有特定的格式要求且当前不受 Blazor 支持，因为所有主流浏览器均不支持它们：</span><span class="sxs-lookup"><span data-stu-id="85cdf-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="85cdf-122">`@bind` 支持 `@bind:culture` 参数，以提供用于分析值并设置值格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="85cdf-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="85cdf-123">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="85cdf-124">`date` 和 `number` 具有可提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="85cdf-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="85cdf-125">本地化</span><span class="sxs-lookup"><span data-stu-id="85cdf-125">Localization</span></span>

Blazor<span data-ttu-id="85cdf-126"> 服务器应用使用[本地化中间件](xref:fundamentals/localization#localization-middleware)进行本地化。</span><span class="sxs-lookup"><span data-stu-id="85cdf-126"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="85cdf-127">中间件为从应用请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="85cdf-128">可使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="85cdf-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="85cdf-129">Cookie</span><span class="sxs-lookup"><span data-stu-id="85cdf-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="85cdf-130">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="85cdf-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="85cdf-131">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="85cdf-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="85cdf-132">为国际化配置链接器 (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="85cdf-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="85cdf-133">默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="85cdf-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="85cdf-134">有关控制链接器行为的详细信息和指南，请参阅 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="85cdf-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="85cdf-135">Cookie</span><span class="sxs-lookup"><span data-stu-id="85cdf-135">Cookies</span></span>

<span data-ttu-id="85cdf-136">本地化区域性 cookie 可以保留用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="85cdf-137">该 cookie 是通过应用主机页 (Pages/Host.cshtml.cs) 的 `OnGet` 方法创建的  。</span><span class="sxs-lookup"><span data-stu-id="85cdf-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="85cdf-138">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="85cdf-139">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="85cdf-140">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 协同使用，因而无法保留区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="85cdf-141">因此，建议的方式是使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="85cdf-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="85cdf-142">如果在本地化 cookie 中保留了区域性，则可以使用任意方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="85cdf-143">如果该应用已经为服务器端 ASP.NET Core 建立了本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="85cdf-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="85cdf-144">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="85cdf-145">在 Blazor 服务器应用中创建包含以下内容的 Pages/Host.cshtml.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="85cdf-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="85cdf-146">本地化由应用按以下事件顺序进行处理：</span><span class="sxs-lookup"><span data-stu-id="85cdf-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="85cdf-147">浏览器向应用发送初始 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="85cdf-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="85cdf-148">本地化中间件分配区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="85cdf-149">_Host.cshtml.cs 中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中  。</span><span class="sxs-lookup"><span data-stu-id="85cdf-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="85cdf-150">浏览器打开 WebSocket 连接以创建交互式 Blazor 服务器会话。</span><span class="sxs-lookup"><span data-stu-id="85cdf-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="85cdf-151">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="85cdf-152">Blazor 服务器会话以正确的区域性开始。</span><span class="sxs-lookup"><span data-stu-id="85cdf-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="85cdf-153">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="85cdf-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="85cdf-154">若要提供支持用户选择区域性的 UI，建议使用基于重定向的方法  。</span><span class="sxs-lookup"><span data-stu-id="85cdf-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="85cdf-155">此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况 &mdash; 用户重定向到登录页，然后重定向回原始资源。</span><span class="sxs-lookup"><span data-stu-id="85cdf-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="85cdf-156">应用通过重定向到控制器来保留用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="85cdf-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="85cdf-157">控制器将用户选择的区域性设置为 cookie，然后将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="85cdf-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="85cdf-158">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="85cdf-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="85cdf-159">使用 `LocalRedirect` 操作结果以阻止开放式重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="85cdf-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="85cdf-160">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="85cdf-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="85cdf-161">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="85cdf-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="85cdf-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="85cdf-162">Additional resources</span></span>

* <xref:fundamentals/localization>
