---
title: ASP.NET Core Blazor 全球化和本地化
author: guardrex
description: 了解如何使 Razor 组件能够供位于不同区域、使用不同语言的用户使用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 8baee9bc0a8e569174f33dac6a406b2162d09552
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279916"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="2001f-103">ASP.NET Core Blazor 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="2001f-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="2001f-104">Razor 组件可供位于不同区域、使用不同语言的用户使用。</span><span class="sxs-lookup"><span data-stu-id="2001f-104">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="2001f-105">以下 .NET 全球化和本地化方案可用：</span><span class="sxs-lookup"><span data-stu-id="2001f-105">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="2001f-106">.NET 资源系统</span><span class="sxs-lookup"><span data-stu-id="2001f-106">.NET's resources system</span></span>
* <span data-ttu-id="2001f-107">特定于区域性的数字和日期格式</span><span class="sxs-lookup"><span data-stu-id="2001f-107">Culture-specific number and date formatting</span></span>

<span data-ttu-id="2001f-108">当前支持有限的 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="2001f-108">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="2001f-109">Blazor 应用中支持 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>。</span><span class="sxs-lookup"><span data-stu-id="2001f-109"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="2001f-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、<xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 和数据注释本地化是 ASP.NET Core MVC 方案，在 Blazor 应用中不受支持。</span><span class="sxs-lookup"><span data-stu-id="2001f-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="2001f-111">有关详细信息，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="2001f-111">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="2001f-112">全球化</span><span class="sxs-lookup"><span data-stu-id="2001f-112">Globalization</span></span>

<span data-ttu-id="2001f-113">Blazor 的 [`@bind`](xref:mvc/views/razor#bind) 功能基于用户的当前区域性执行格式并分析值以进行显示。</span><span class="sxs-lookup"><span data-stu-id="2001f-113">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="2001f-114">可从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-114">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="2001f-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> 用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="2001f-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="2001f-116">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="2001f-116">The preceding field types:</span></span>

* <span data-ttu-id="2001f-117">使用其基于浏览器的相应格式规则进行显示。</span><span class="sxs-lookup"><span data-stu-id="2001f-117">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="2001f-118">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="2001f-118">Can't contain free-form text.</span></span>
* <span data-ttu-id="2001f-119">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="2001f-119">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="2001f-120">以下字段类型具有特定的格式要求且当前不受 Blazor 支持，因为所有主流浏览器均不支持它们：</span><span class="sxs-lookup"><span data-stu-id="2001f-120">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="2001f-121">[`@bind`](xref:mvc/views/razor#bind) 支持 `@bind:culture` 参数，以提供用于分析值并设置值格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="2001f-121">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="2001f-122">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-122">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="2001f-123">`date` 和 `number` 具有可提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="2001f-123">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="2001f-124">本地化</span><span class="sxs-lookup"><span data-stu-id="2001f-124">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="2001f-125">Blazor WebAssembly 应用使用用户的[语言首选项](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)设置区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-125">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="2001f-126">若要显式配置区域性，请在 `Program.Main` 中设置 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> 和 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="2001f-126">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2001f-127">默认情况下，Blazor WebAssembly 携带在用户区域性中显示值（如日期和货币）所需的最小全球化资源。</span><span class="sxs-lookup"><span data-stu-id="2001f-127">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="2001f-128">必须支持动态更改区域性的应用程序应在项目文件中配置 `BlazorWebAssemblyLoadAllGlobalizationData`：</span><span class="sxs-lookup"><span data-stu-id="2001f-128">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="2001f-129">还可以通过传递给 `Blazor.start` 的选项将 Blazor WebAssembly 配置为使用特定应用程序区域性启动。</span><span class="sxs-lookup"><span data-stu-id="2001f-129">Blazor WebAssembly can also be configured to launch using a specific application culture using options passed to `Blazor.start`.</span></span> <span data-ttu-id="2001f-130">例如，下面的示例显示配置为使用 `en-GB` 区域性启动的应用：</span><span class="sxs-lookup"><span data-stu-id="2001f-130">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="2001f-131">`applicationCulture` 的值应符合 [BCP-47 语言标记格式](https://tools.ietf.org/html/bcp47)。</span><span class="sxs-lookup"><span data-stu-id="2001f-131">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="2001f-132">如果应用不需要本地化，你可以将应用配置为支持不变区域性，这基于 `en-US` 区域性：</span><span class="sxs-lookup"><span data-stu-id="2001f-132">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2001f-133">默认情况下，Blazor WebAssembly 应用的中间语言 (IL) 链接器配置去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="2001f-133">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="2001f-134">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="2001f-134">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="2001f-135">虽然 Blazor 默认选择的区域性可能足以满足大多数用户的需求，但请考虑为用户提供一种指定其首选区域设置的方法。</span><span class="sxs-lookup"><span data-stu-id="2001f-135">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="2001f-136">如需获取具有区域性选取器的 Blazor WebAssembly 示例应用，请参阅 [`LocSample`](https://github.com/pranavkm/LocSample) 本地化示例应用。</span><span class="sxs-lookup"><span data-stu-id="2001f-136">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="2001f-137">Blazor Server 应用使用[本地化中间件](xref:fundamentals/localization#localization-middleware)进行本地化。</span><span class="sxs-lookup"><span data-stu-id="2001f-137">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="2001f-138">中间件为从应用请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-138">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="2001f-139">可使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="2001f-139">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="2001f-140">[Cookie](#cookies)</span><span class="sxs-lookup"><span data-stu-id="2001f-140">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="2001f-141">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="2001f-141">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="2001f-142">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="2001f-142">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="2001f-143">Cookies</span><span class="sxs-lookup"><span data-stu-id="2001f-143">Cookies</span></span>

<span data-ttu-id="2001f-144">本地化区域性 cookie 可以保留用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-144">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="2001f-145">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-145">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="2001f-146">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-146">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="2001f-147">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 协同使用，因而无法保留区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-147">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="2001f-148">因此，建议的方式是使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="2001f-148">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="2001f-149">如果在本地化 cookie 中保留了区域性，则可以使用任意方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-149">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="2001f-150">如果该应用已经为服务器端 ASP.NET Core 建立了本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="2001f-150">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="2001f-151">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-151">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="2001f-152">在 `Pages/_Host.cshtml` 文件中的开始 `<body>` 标记内立即创建一个 Razor 表达式：</span><span class="sxs-lookup"><span data-stu-id="2001f-152">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="2001f-153">本地化由应用按以下事件顺序进行处理：</span><span class="sxs-lookup"><span data-stu-id="2001f-153">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="2001f-154">浏览器向应用发送初始 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="2001f-154">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="2001f-155">本地化中间件分配区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-155">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="2001f-156">`_Host` 页面 (`_Host.cshtml`) 中的 Razor 表达式将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="2001f-156">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="2001f-157">浏览器打开 WebSocket 连接以创建交互式 Blazor Server 会话。</span><span class="sxs-lookup"><span data-stu-id="2001f-157">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="2001f-158">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-158">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="2001f-159">Blazor Server 会话以正确的区域性开始。</span><span class="sxs-lookup"><span data-stu-id="2001f-159">The Blazor Server session begins with the correct culture.</span></span>

<span data-ttu-id="2001f-160">使用 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage> 时，请使用 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> 属性：</span><span class="sxs-lookup"><span data-stu-id="2001f-160">When working with a <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>, use the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> property:</span></span>

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="2001f-161">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="2001f-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="2001f-162">若要提供支持用户选择区域性的 UI，建议使用基于重定向的方法。</span><span class="sxs-lookup"><span data-stu-id="2001f-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="2001f-163">此过程与用户尝试访问安全资源时 Web 应用中发生的情况类似。</span><span class="sxs-lookup"><span data-stu-id="2001f-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="2001f-164">用户会被重定向到登录页面，然后再重定向到原始资源。</span><span class="sxs-lookup"><span data-stu-id="2001f-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="2001f-165">应用通过重定向到控制器来保留用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="2001f-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="2001f-166">控制器将用户选择的区域性设置为 cookie，然后将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="2001f-166">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="2001f-167">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="2001f-167">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="2001f-168">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 操作结果以阻止开放式重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="2001f-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="2001f-169">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="2001f-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="2001f-170">如果应用未配置为处理控制器操作：</span><span class="sxs-lookup"><span data-stu-id="2001f-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="2001f-171">将 MVC 服务添加到 `Startup.ConfigureServices` 中的服务集合：</span><span class="sxs-lookup"><span data-stu-id="2001f-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="2001f-172">在 `Startup.Configure` 中添加控制器终结点路由：</span><span class="sxs-lookup"><span data-stu-id="2001f-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="2001f-173">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="2001f-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="2001f-174">其他资源</span><span class="sxs-lookup"><span data-stu-id="2001f-174">Additional resources</span></span>

* <xref:fundamentals/localization>
