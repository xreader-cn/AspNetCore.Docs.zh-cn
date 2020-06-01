---
<span data-ttu-id="05551-101">title:“ASP.NET Core Blazor 全球化和本地化”author: description:“了解如何使 Razor 组件能够供位于不同区域、使用不同语言的用户使用。”</span><span class="sxs-lookup"><span data-stu-id="05551-101">title: 'ASP.NET Core Blazor globalization and localization' author: description: 'Learn how to make Razor components accessible to users in multiple cultures and languages.'</span></span>
<span data-ttu-id="05551-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="05551-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="05551-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="05551-103">'Blazor'</span></span>
- <span data-ttu-id="05551-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="05551-104">'Identity'</span></span>
- <span data-ttu-id="05551-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="05551-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="05551-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="05551-106">'Razor'</span></span>
- <span data-ttu-id="05551-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="05551-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="05551-108">ASP.NET Core Blazor 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="05551-108">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="05551-109">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="05551-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="05551-110"> 组件可供位于不同区域、使用不同语言的用户使用。</span><span class="sxs-lookup"><span data-stu-id="05551-110"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="05551-111">以下 .NET 全球化和本地化方案可用：</span><span class="sxs-lookup"><span data-stu-id="05551-111">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="05551-112">.NET 资源系统</span><span class="sxs-lookup"><span data-stu-id="05551-112">.NET's resources system</span></span>
* <span data-ttu-id="05551-113">特定于区域性的数字和日期格式</span><span class="sxs-lookup"><span data-stu-id="05551-113">Culture-specific number and date formatting</span></span>

<span data-ttu-id="05551-114">当前支持有限的 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="05551-114">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="05551-115">Blazor 应用中支持 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>。</span><span class="sxs-lookup"><span data-stu-id="05551-115"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="05551-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、<xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 和数据注释本地化是 ASP.NET Core MVC 方案，在 Blazor 应用中不受支持。</span><span class="sxs-lookup"><span data-stu-id="05551-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="05551-117">有关详细信息，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="05551-117">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="05551-118">全球化</span><span class="sxs-lookup"><span data-stu-id="05551-118">Globalization</span></span>

Blazor<span data-ttu-id="05551-119"> 的 [`@bind`](xref:mvc/views/razor#bind) 功能基于用户的当前区域性执行格式并分析值以进行显示。</span><span class="sxs-lookup"><span data-stu-id="05551-119">'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="05551-120">可从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-120">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="05551-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> 用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="05551-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="05551-122">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="05551-122">The preceding field types:</span></span>

* <span data-ttu-id="05551-123">使用其基于浏览器的相应格式规则进行显示。</span><span class="sxs-lookup"><span data-stu-id="05551-123">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="05551-124">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="05551-124">Can't contain free-form text.</span></span>
* <span data-ttu-id="05551-125">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="05551-125">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="05551-126">以下字段类型具有特定的格式要求且当前不受 Blazor 支持，因为所有主流浏览器均不支持它们：</span><span class="sxs-lookup"><span data-stu-id="05551-126">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="05551-127">[`@bind`](xref:mvc/views/razor#bind) 支持 `@bind:culture` 参数，以提供用于分析值并设置值格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="05551-127">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="05551-128">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-128">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="05551-129">`date` 和 `number` 具有可提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="05551-129">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="05551-130">本地化</span><span class="sxs-lookup"><span data-stu-id="05551-130">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="05551-131"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="05551-131"> WebAssembly</span></span>

Blazor<span data-ttu-id="05551-132"> WebAssembly 应用使用用户的[语言首选项](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)设置区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-132"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="05551-133">若要显式配置区域性，请在 `Program.Main` 中设置 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> 和 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="05551-133">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="05551-134">默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="05551-134">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="05551-135">有关控制链接器行为的详细信息和指南，请参阅 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="05551-135">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="05551-136">虽然 Blazor 默认选择的区域性可能足以满足大多数用户的需求，但请考虑为用户提供一种指定其首选区域设置的方法。</span><span class="sxs-lookup"><span data-stu-id="05551-136">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="05551-137">如需获取具有区域性选取器的 Blazor WebAssembly 示例应用，请参阅 [LocSample](https://github.com/pranavkm/LocSample) 本地化示例应用。</span><span class="sxs-lookup"><span data-stu-id="05551-137">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="05551-138"> 服务器</span><span class="sxs-lookup"><span data-stu-id="05551-138"> Server</span></span>

Blazor<span data-ttu-id="05551-139"> 服务器应用使用[本地化中间件](xref:fundamentals/localization#localization-middleware)进行本地化。</span><span class="sxs-lookup"><span data-stu-id="05551-139"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="05551-140">中间件为从应用请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-140">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="05551-141">可使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="05551-141">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="05551-142">Cookie</span><span class="sxs-lookup"><span data-stu-id="05551-142">Cookies</span></span>](#cookies)
* [<span data-ttu-id="05551-143">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="05551-143">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="05551-144">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="05551-144">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="05551-145">Cookie</span><span class="sxs-lookup"><span data-stu-id="05551-145">Cookies</span></span>

<span data-ttu-id="05551-146">本地化区域性 cookie 可以保留用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-146">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="05551-147">该 cookie 是通过应用主机页 (Pages/Host.cshtml.cs) 的 `OnGet` 方法创建的。</span><span class="sxs-lookup"><span data-stu-id="05551-147">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="05551-148">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-148">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="05551-149">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-149">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="05551-150">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 协同使用，因而无法保留区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-150">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="05551-151">因此，建议的方式是使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="05551-151">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="05551-152">如果在本地化 cookie 中保留了区域性，则可以使用任意方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-152">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="05551-153">如果该应用已经为服务器端 ASP.NET Core 建立了本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="05551-153">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="05551-154">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-154">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="05551-155">在 Blazor Server 应用中创建包含以下内容的 Pages/_Host.cshtml.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="05551-155">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="05551-156">本地化由应用按以下事件顺序进行处理：</span><span class="sxs-lookup"><span data-stu-id="05551-156">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="05551-157">浏览器向应用发送初始 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="05551-157">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="05551-158">本地化中间件分配区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-158">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="05551-159">_Host.cshtml.cs 中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="05551-159">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="05551-160">浏览器打开 WebSocket 连接以创建交互式 Blazor 服务器会话。</span><span class="sxs-lookup"><span data-stu-id="05551-160">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="05551-161">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-161">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="05551-162">Blazor 服务器会话以正确的区域性开始。</span><span class="sxs-lookup"><span data-stu-id="05551-162">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="05551-163">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="05551-163">Provide UI to choose the culture</span></span>

<span data-ttu-id="05551-164">若要提供支持用户选择区域性的 UI，建议使用基于重定向的方法。</span><span class="sxs-lookup"><span data-stu-id="05551-164">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="05551-165">此过程与用户尝试访问安全资源时 Web 应用中发生的情况类似。</span><span class="sxs-lookup"><span data-stu-id="05551-165">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="05551-166">用户会被重定向到登录页面，然后再重定向到原始资源。</span><span class="sxs-lookup"><span data-stu-id="05551-166">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="05551-167">应用通过重定向到控制器来保留用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="05551-167">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="05551-168">控制器将用户选择的区域性设置为 cookie，然后将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="05551-168">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="05551-169">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="05551-169">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="05551-170">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 操作结果以阻止开放式重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="05551-170">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="05551-171">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="05551-171">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="05551-172">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="05551-172">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="05551-173">其他资源</span><span class="sxs-lookup"><span data-stu-id="05551-173">Additional resources</span></span>

* <xref:fundamentals/localization>
