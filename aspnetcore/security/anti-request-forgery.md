---
title: 在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击
author: steve-smith
description: 了解如何防止恶意网站可能会影响客户端浏览器与应用程序之间的交互的 web 应用程序的攻击。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: security/anti-request-forgery
ms.openlocfilehash: 54e153af55f28d9a89bbf16bce1c17f876567b59
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880811"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)和[Steve Smith](https://ardalis.com/)

跨站点请求伪造（也称为 XSRF 或 CSRF）是一种针对 web 托管应用的攻击，恶意 web 应用可能会影响客户端浏览器与信任该浏览器的 web 应用之间的交互。 这些攻击是可能的，因为 web 浏览器会自动向网站发送每个请求的身份验证令牌。 这种形式的攻击也称为*一键式攻击*或*会话 riding* ，因为攻击利用用户以前的经过身份验证的会话。

CSRF 攻击的示例：

1. 用户使用窗体身份验证登录 `www.good-banking-site.com`。 服务器对用户进行身份验证，并发出包含身份验证 cookie 的响应。 此站点容易受到攻击，因为它信任使用有效的身份验证 cookie 接收的任何请求。
1. 用户访问恶意网站 `www.bad-crook-site.com`。

   恶意网站 `www.bad-crook-site.com`包含如下所示的 HTML 格式：

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   请注意，窗体的 `action` 会发布到易受攻击的站点，而不是恶意站点。 这是 CSRF 的 "跨站点" 部分。

1. 用户选择 "提交" 按钮。 浏览器发出请求并自动包含请求域的身份验证 cookie，`www.good-banking-site.com`。
1. 请求使用用户的身份验证上下文在 `www.good-banking-site.com` 服务器上运行，并可执行允许经过身份验证的用户执行的任何操作。

除了用户选择按钮以提交窗体的情况外，恶意网站还可以：

* 运行自动提交窗体的脚本。
* 以 AJAX 请求形式发送窗体提交。
* 使用 CSS 隐藏窗体。

除最初访问恶意网站外，这些备选方案不需要用户执行任何操作或输入。

使用 HTTPS 不会阻止 CSRF 攻击。 可以发送恶意站点 `https://www.good-banking-site.com/` 请求一样方便它可以发送不安全的请求。

某些攻击目标是响应 GET 请求的终结点，在这种情况下，可以使用图像标记执行操作。 这种形式的攻击在允许图像但阻止 JavaScript 的论坛站点上很常见。 在 GET 请求上更改状态的应用（其中变量或资源被更改）很容易受到恶意攻击。 **更改状态的 GET 请求是不安全的。最佳做法是从不更改 GET 请求的状态。**

对于使用 cookie 进行身份验证的 web 应用，可能会受到 CSRF 攻击，因为：

* 浏览器存储 web 应用颁发的 cookie。
* 存储的 cookie 包括经过身份验证的用户的会话 cookie。
* 无论在浏览器中生成应用程序请求的方式如何，浏览器都会将与域关联的所有 cookie 发送到 web 应用。

但是，CSRF 攻击并不局限于利用 cookie。 例如，基本身份验证和摘要式身份验证也容易受到攻击。 用户使用基本或摘要式身份验证登录后，浏览器会自动发送凭据，直到会话&dagger; 结束。

&dagger;在此上下文中， *session*是指在其中对用户进行身份验证的客户端会话。 它是与服务器端会话无关或[ASP.NET Core 会话中间件](xref:fundamentals/app-state)。

用户可以采取预防措施来防止 CSRF 漏洞：

* 使用完 web 应用后，将其注销。
* 定期清除浏览器 cookie。

但是，CSRF 漏洞在本质上是 web 应用的问题，而不是最终用户的问题。

## <a name="authentication-fundamentals"></a>身份验证基础知识

基于 Cookie 的身份验证是一种常用的身份验证形式。 基于令牌的身份验证系统不断增长，尤其是对于单页面应用程序（Spa）。

### <a name="cookie-based-authentication"></a>基于 Cookie 的身份验证

用户使用其用户名和密码进行身份验证时，将颁发令牌，其中包含可用于身份验证和授权的身份验证票证。 令牌存储为每个客户端发出的请求附带的 cookie。 生成和验证此 cookie 由 Cookie 身份验证中间件来执行。 [中间件](xref:fundamentals/middleware/index)将用户主体序列化为加密的 cookie。 在后续请求中，中间件将验证 cookie，重新创建主体，并将主体分配给[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)的[用户](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)属性。

### <a name="token-based-authentication"></a>基于令牌的身份验证

在对用户进行身份验证时，他们将颁发一个令牌（而不是防伪令牌）。 令牌包含[声明](/dotnet/framework/security/claims-based-identity-model)形式的用户信息或引用令牌，该令牌将应用指向应用中维护的用户状态。 当用户尝试访问要求身份验证的资源时，会将令牌发送到应用程序，并以持有者令牌的形式提供附加的授权标头。 这使应用无状态。 在每个后续请求中，将在请求服务器端验证时传递该令牌。 此标记未*加密*;*编码*。 在服务器上，将解码令牌来访问其信息。 若要在后续请求中发送令牌，请将该令牌存储在浏览器的本地存储中。 如果令牌存储在浏览器的本地存储中，请不要担心 CSRF 漏洞。 如果标记存储在 cookie 中，则需要考虑 CSRF。 有关详细信息，请参阅 GitHub 问题[SPA 代码示例添加两个 cookie](https://github.com/aspnet/AspNetCore.Docs/issues/13369)。

### <a name="multiple-apps-hosted-at-one-domain"></a>在一个域中托管多个应用

共享宿主环境容易遭受会话劫持、登录 CSRF 和其他攻击。

尽管 `example1.contoso.net` 和 `example2.contoso.net` 是不同主机，但 `*.contoso.net` 域下的主机之间存在隐式信任关系。 这种隐式信任关系允许可能不受信任的主机影响彼此的 cookie （控制 AJAX 请求的相同来源策略并不一定适用于 HTTP cookie）。

如果不共享域，则可以阻止利用同一域中托管的应用之间的受信任 cookie 的攻击。 当每个应用托管在其自己的域中时，没有要利用的隐式 cookie 信任关系。

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core antiforgery 配置

> [!WARNING]
> ASP.NET Core 实现 antiforgery 使用[ASP.NET Core 数据保护](xref:security/data-protection/introduction)。 必须将数据保护堆栈配置为在服务器场中运行。 有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。

::: moniker range=">= aspnetcore-3.0"

在 `Startup.ConfigureServices`中调用以下某个 Api 时，会将防伪中间件添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器中：

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

当在中调用 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 时，防伪中间件将添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器 `Startup.ConfigureServices`

::: moniker-end

在 ASP.NET Core 2.0 或更高版本， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antiforgery 令牌注入 HTML 窗体元素。 Razor 文件中的以下标记会自动生成防伪标记：

```cshtml
<form method="post">
    ...
</form>
```

同样，如果窗体的方法不获取，则默认情况下， [IHtmlHelper](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)将生成防伪标记。

当 `<form>` 标记包含 `method="post"` 属性并且满足以下任一条件时，会自动生成 HTML 窗体元素的防伪令牌：

* 操作属性为空（`action=""`）。
* 未提供操作属性（`<form method="post">`）。

可以禁用 HTML 窗体元素的自动生成防伪标记：

* 显式禁用 `asp-antiforgery` 属性的防伪令牌：

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Form 元素使用 Tag Helper [！ opt out 符号](xref:mvc/views/tag-helpers/intro#opt-out)选择退出标记帮助器：

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* 从视图中删除 `FormTagHelper`。 可以通过将以下指令添加到 Razor 视图来从视图中删除 `FormTagHelper`：

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> 自动从 XSRF/CSRF 保护[Razor Pages](xref:razor-pages/index) 。 有关详细信息，请参阅[XSRF/CSRF 和 Razor Pages](xref:razor-pages/index#xsrf)。

防御 CSRF 攻击的最常见方法是使用*同步器令牌模式*（STP）。 当用户请求包含窗体数据的页面时，将使用 STP：

1. 服务器向客户端发送与当前用户的标识关联的令牌。
1. 客户端将该令牌发送回服务器以进行验证。
1. 如果服务器收到的令牌与经过身份验证的用户的标识不匹配，则会拒绝该请求。

该令牌是唯一的且不可预测的。 令牌还可用于确保对一系列请求进行正确排序（例如，确保的请求序列为： page 1 &ndash; 第2页 &ndash; 第3页）。 ASP.NET Core MVC 和 Razor 页模板中的窗体的所有生成 antiforgery 令牌。 以下对视图示例将生成防伪令牌：

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

将防伪标记显式添加到 `<form>` 元素，而不将标记帮助程序与 HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)一起使用：

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

在每个前面的情况下，ASP.NET Core 添加类似于以下一个隐藏的表单字段：

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core 包括三个[筛选器](xref:mvc/controllers/filters)来处理 antiforgery 令牌：

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>防伪选项

自定义 `Startup.ConfigureServices`中的[防伪选项](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)：

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;使用[CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)类的属性设置防伪 `Cookie` 属性。

| 选项 | 描述 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 确定用于创建防伪 cookie 的设置。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 防伪系统使用的标头的名称。 如果 `null`，系统只考虑窗体数据。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否取消生成 `X-Frame-Options` 标头。 默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。 默认为 `false`。 |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| 选项 | 描述 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 确定用于创建防伪 cookie 的设置。 |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Cookie 的域。 默认为 `null`。 此属性已过时，并将在将来的版本中删除。 建议的替代项为 Cookie。 |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Cookie 的名称。 如果未设置，系统将生成一个以[DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) （"。AspNetCore. 防伪. "）。 此属性已过时，并将在将来的版本中删除。 建议的替代项为 Cookie.Name。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Cookie 上设置的路径。 此属性已过时，并将在将来的版本中删除。 建议的替代项为 Cookie。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 防伪系统使用的标头的名称。 如果 `null`，系统只考虑窗体数据。 |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 指定防伪系统是否需要 HTTPS。 如果 `true`，则非 HTTPS 请求会失败。 默认为 `false`。 此属性已过时，并将在将来的版本中删除。 建议的替代项为设置 SecurePolicy。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否取消生成 `X-Frame-Options` 标头。 默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。 默认为 `false`。 |

::: moniker-end

有关详细信息，请参阅[cookieauthenticationoptions.authenticationtype](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。

## <a name="configure-antiforgery-features-with-iantiforgery"></a>通过 IAntiforgery 配置防伪功能

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)提供用于配置防伪功能的 API。 可在 `Startup` 类的 `Configure` 方法中请求 `IAntiforgery`。 下面的示例使用应用程序主页中的中间件来生成防伪标记，并将其作为 cookie 发送到响应中（使用本主题后面部分介绍的默认角度命名约定）：

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>需要防伪验证

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)是一个可应用于单个操作、控制器或全局的操作筛选器。 将阻止对应用了此筛选器的操作发出的请求，除非该请求包含有效的防伪令牌。

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

`ValidateAntiForgeryToken` 特性要求对其标记的操作方法（包括 HTTP GET 请求）发出请求的令牌。 如果在应用的控制器上应用 `ValidateAntiForgeryToken` 属性，则可以通过 `IgnoreAntiforgeryToken` 属性将其重写。

> [!NOTE]
> ASP.NET Core 不支持自动将 antiforgery 令牌添加到 GET 请求。

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>仅自动验证不安全 HTTP 方法的防伪令牌

ASP.NET Core 应用不生成 antiforgery 令牌进行安全的 HTTP 方法 （GET、 HEAD、 选项和跟踪）。 可以使用[AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性，而不是广泛应用 `ValidateAntiForgeryToken` 属性，然后使用 `IgnoreAntiforgeryToken` 属性将其重写。 此特性与 `ValidateAntiForgeryToken` 特性的工作方式相同，不同之处在于，它不需要使用以下 HTTP 方法发出的请求的令牌：

* GET
* HEAD
* 选项
* TRACE

建议为非 API 方案广泛使用 `AutoValidateAntiforgeryToken`。 这可确保默认情况下保护后操作。 另一种方法是默认忽略防伪标记，除非将 `ValidateAntiForgeryToken` 应用到各个操作方法。 在这种情况下，更有可能在此方案中，不受错误阻止的 POST 操作方法，使应用容易受到 CSRF 攻击。 所有Post请求都应发送防伪令牌。

Api 没有用于发送令牌的非 cookie 部分的自动机制。 实现可能取决于客户端代码实现。 下面显示了一些示例：

类级别的示例：

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

全局示例：

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>重写全局或控制器防伪属性

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)筛选器用于消除给定操作（或控制器）的防伪标记的需要。 应用此筛选器时，会覆盖在更高级别（全局或控制器上）指定 `ValidateAntiForgeryToken` 和 `AutoValidateAntiforgeryToken` 筛选器。

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>身份验证后刷新令牌

通过将用户重定向到视图或 Razor Pages 页面来对用户进行身份验证后，应刷新标记。

## <a name="javascript-ajax-and-spas"></a>JavaScript、AJAX 和 Spa

在传统的基于 HTML 的应用程序中，使用隐藏的窗体字段将防伪令牌传递给服务器。 在基于 JavaScript 的新式应用和 Spa 中，许多请求是以编程方式进行的。 这些 AJAX 请求可能使用其他技术（例如请求标头或 cookie）来发送令牌。

如果使用 cookie 来存储身份验证令牌，并在服务器上对 API 请求进行身份验证，则 CSRF 是一个潜在问题。 如果使用本地存储来存储令牌，可能会降低 CSRF 漏洞，因为本地存储中的值不会随每个请求自动发送到服务器。 因此，使用本地存储在客户端上存储防伪令牌并将令牌作为请求标头进行发送是建议的方法。

### <a name="javascript"></a>JavaScript

将 JavaScript 用于视图，可以使用视图中的服务创建令牌。 将[AspNetCore 防伪 IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)服务插入到视图中，并调用[GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)：

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

此方法无需直接处理来自服务器的 cookie 设置，也无需从客户端读取它。

前面的示例使用 JavaScript 读取 AJAX POST 标头的隐藏字段值。

JavaScript 还可以访问 cookie 中的令牌，并使用 cookie 的内容创建带有令牌值的标头。

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

假设脚本请求将令牌发送到名为 `X-CSRF-TOKEN`的标头，请将防伪服务配置为查找 `X-CSRF-TOKEN` 标头：

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

下面的示例使用 JavaScript 通过适当的标头发出 AJAX 请求：

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS 使用约定来寻址 CSRF。 如果服务器发送 `XSRF-TOKEN`名称的 cookie，则在向服务器发送请求时，AngularJS `$http` 服务会将 cookie 值添加到标头。 此过程是自动进行的。 不需要在客户端中显式设置标头。 标头名称为 `X-XSRF-TOKEN`。 服务器应检测此标头并验证其内容。

对于在应用程序启动时使用此约定的 ASP.NET Core API：

* 将应用配置为在名为 `XSRF-TOKEN`的 cookie 中提供令牌。
* 配置防伪服务以查找名为 `X-XSRF-TOKEN`的标头。

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="extend-antiforgery"></a>扩展防伪

[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)类型允许开发人员通过往返每个标记中的其他数据来扩展反 CSRF 系统的行为。 每次生成字段标记时都会调用[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)方法，并且返回值嵌入到生成的标记中。 在验证令牌时，实施者可以返回时间戳、nonce 或任何其他值，然后调用[ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)来验证此数据。 客户端的用户名已嵌入到生成的令牌中，因此不需要包含此信息。 如果令牌包含补充数据但未配置 `IAntiForgeryAdditionalDataProvider`，则不会验证补充数据。

## <a name="additional-resources"></a>其他资源

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) [打开 Web 应用程序安全项目](https://www.owasp.org/index.php/Main_Page)（OWASP）。
* <xref:host-and-deploy/web-farm>
