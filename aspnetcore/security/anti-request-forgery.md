---
title: 在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击
author: steve-smith
description: 了解如何防止攻击，恶意网站可以影响客户端浏览器和应用之间的交互的 web 应用。
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: a32e0e2dbd7fab95562a562cb88767d4c1e8049d
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458494"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击

通过[Steve Smith](https://ardalis.com/)， [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

跨站点请求伪造 （也称为 XSRF 或 CSRF） 是针对恶意网站凭此可以影响客户端浏览器和信任该浏览器的 web 应用之间的交互的 web 托管的应用程序的攻击。 这些攻击是可能的因为 web 浏览器将自动随每个请求某些类型的身份验证令牌发送到网站。 这种形式的攻击是也称为*一键式攻击*或*会话行进*因为此攻击利用用户的身份验证会话。

CSRF 攻击的示例：

1. 用户登录到`www.good-banking-site.com`使用窗体身份验证。 服务器对用户进行身份验证，并会发出包含身份验证 cookie 的响应。 站点是易受到攻击，因为它信任的任何请求都收到与有效的身份验证 cookie。
1. 在用户访问恶意站点， `www.bad-crook-site.com`。

   恶意网站`www.bad-crook-site.com`，包含 HTML 窗体如下所示：

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   请注意，窗体的`action`帖子到易受攻击的站点上，而不是恶意站点。 这是 CSRF 的"跨站点"部分。

1. 用户选择提交按钮。 浏览器发出请求，并会自动包括请求的域的身份验证 cookie `www.good-banking-site.com`。
1. 运行该请求`www.good-banking-site.com`与用户的身份验证上下文的服务器，并且可以执行身份验证的用户可以执行任何操作。

除了其中用户选择按钮以提交窗体方案中，恶意网站可以：

* 运行自动提交窗体的脚本。
* AJAX 请求用以发送窗体提交。
* 隐藏窗体使用 CSS。

这些替代方案不需要任何操作或从用户而不是最初访问恶意网站的输入。

使用 HTTPS，则不会阻止 CSRF 攻击。 可以发送恶意站点 `https://www.good-banking-site.com/` 请求一样方便它可以发送不安全的请求。

一些攻击目标终结点的响应 GET 请求，在这种情况下的图像标记可用于执行的操作。 这种形式的攻击的允许的映像，但阻止 JavaScript 论坛站点上很常见。 应用程序来更改的状态的 GET 请求，在其中更改变量或资源，是很容易受到恶意攻击。 **更改状态的 GET 请求将不安全。最佳做法是永远不会更改上的 GET 请求的状态。**

CSRF 攻击是可能使用 cookie 进行身份验证，因为 web 应用：

* 浏览器存储 cookie 颁发的 web 应用。
* 存储的 cookie 包括用于身份验证的用户的会话 cookie。
* 浏览器发送的所有 cookie 与域关联到 web 应用而不考虑对应用程序的请求如何生成在浏览器中的每个请求。

但是，到限于 CSRF 攻击利用 cookie。 例如，基本和摘要式身份验证也是易受攻击的。 浏览器使用基本或摘要式身份验证的用户登录后，直到在会话自动发送凭据&dagger;结束。

&dagger;在此上下文中，*会话*指的是在此期间用户进行身份验证的客户端会话。 它是与服务器端会话无关或[ASP.NET Core 会话中间件](xref:fundamentals/app-state)。

用户可以通过采取预防措施来防止 CSRF 漏洞：

* 从 web 应用时将它们完成相应操作进行签名。
* 定期清除浏览器 cookie。

但是，CSRF 漏洞从根本上说是 web 应用，而不是最终用户的问题。

## <a name="authentication-fundamentals"></a>身份验证基础知识

基于 cookie 的身份验证是常用形式的身份验证。 基于令牌的身份验证系统越来越流行，尤其是对于单页面应用程序 (Spa)。

### <a name="cookie-based-authentication"></a>基于 cookie 的身份验证

当用户通过使用其用户名和密码时，它们被颁发一个令牌，其中包含可用于身份验证和授权的身份验证票证。 作为一个附带的每个请求客户端的 cookie 会使存储令牌。 由 Cookie 身份验证中间件执行生成和验证此 cookie。 [中间件](xref:fundamentals/middleware/index)的加密 cookie 序列化为一个用户主体。 在后续请求中，中间件将验证 cookie、 重新创建主体，并将分配到主体[用户](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)的属性[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)。

### <a name="token-based-authentication"></a>基于令牌的身份验证

当用户进行身份验证时，它们被颁发的令牌 （不防伪令牌）。 该令牌包含的窗体中的用户信息[声明](/dotnet/framework/security/claims-based-identity-model)或指向用户状态保留在应用程序的应用程序的引用标记。 当用户尝试访问资源需要身份验证时，则会将令牌发送到其他 authorization 标头中的持有者令牌的窗体应用。 这使得应用程序无状态。 在每个后续请求的令牌请求中传递的服务器端验证。 此令牌不是*加密*; 它具有*编码*。 在服务器上，将解码令牌来访问其信息。 若要在后续请求中发送令牌，请在浏览器的本地存储中存储令牌。 如果该令牌存储在浏览器的本地存储，则不会担心 CSRF 的漏洞。 CSRF 令牌存储在 cookie 中时是一个问题。

### <a name="multiple-apps-hosted-at-one-domain"></a>在一个域上托管的多个应用

共享宿主环境为易受到会话劫持、 登录名 CSRF 和其他攻击。

尽管`example1.contoso.net`并`example2.contoso.net`是不同的主机，在主机之间没有隐式信任关系`*.contoso.net`域。 此隐式信任关系允许可能不受信任的主机会影响彼此的 cookie （控制 AJAX 请求的同域策略不一定适用于 HTTP cookie）。

通过不共享域，可以防止攻击，可利用同一个域上托管的应用程序之间的受信任的 cookie。 在其自己的域中托管每个应用，则时利用任何隐式 cookie 信任关系。

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core antiforgery 配置

> [!WARNING]
> ASP.NET Core 实现 antiforgery 使用[ASP.NET Core 数据保护](xref:security/data-protection/introduction)。 数据保护堆栈必须配置为在服务器场中工作。 请参阅[配置数据保护](xref:security/data-protection/configuration/overview)有关详细信息。

在 ASP.NET Core 2.0 或更高版本， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antiforgery 令牌注入 HTML 窗体元素。 Razor 文件中的以下标记自动生成防伪令牌：

```cshtml
<form method="post">
    ...
</form>
```

同样， [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)默认情况下生成防伪令牌，如果窗体的方法不是 GET。

HTML 窗体元素自动生成的防伪令牌发生时`<form>`标记包含`method="post"`属性和以下任一条件成立：

* 操作属性为空 (`action=""`)。
* 操作属性不提供 (`<form method="post">`)。

可以禁用自动生成的防伪标记 HTML 窗体元素：

* 显式禁用防伪令牌，且`asp-antiforgery`属性：

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* 窗体元素是选择向外的标记帮助程序使用标记帮助程序[！ 选择退出符号](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* 删除`FormTagHelper`从视图。 `FormTagHelper`可以通过将以下指令添加到 Razor 视图从视图中删除：

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor 页面](xref:razor-pages/index)会自动防范 XSRF/CSRF。 有关详细信息，请参阅[XSRF/CSRF 和 Razor 页面](xref:razor-pages/index#xsrf)。

为抵御 CSRF 攻击最常用的方法是使用*同步器标记模式*(STP)。 当用户请求的页面包含窗体数据使用 STP:

1. 服务器发送到客户端的当前用户的标识相关联的令牌。
1. 客户端返回将令牌发送到服务器进行验证。
1. 如果服务器收到与经过身份验证的用户的标识不匹配的令牌，将拒绝请求。

该令牌唯一且不可预测。 该令牌还可用于确保正确序列化的一系列的请求 (例如，确保请求序列的： 第 1 页&ndash;第 2 页&ndash;第 3 页)。 ASP.NET Core MVC 和 Razor 页模板中的窗体的所有生成 antiforgery 令牌。 以下两个视图的示例生成防伪令牌：

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

显式添加到防伪令牌`<form>`而无需使用标记帮助程序与 HTML 帮助程序元素[ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

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

自定义[防伪选项](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)中`Startup.ConfigureServices`:

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

&dagger;设置防伪`Cookie`属性使用的属性[CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)类。

| 选项 | 描述 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 确定用于创建防伪 cookie 的设置。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 防伪系统用于呈现防伪令牌在视图中的隐藏的窗体字段的名称。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 防伪系统使用的标头的名称。 如果`null`，系统会认为只有窗体数据。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否禁止显示生成`X-Frame-Options`标头。 默认情况下，值为"SAMEORIGIN"生成标头。 默认为 `false`。 |

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
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Cookie 的域。 默认为 `null`。 此属性已过时，将在未来版本中删除。 建议的替代项是 Cookie.Domain。 |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Cookie 的名称。 如果未设置，则系统生成唯一的名称开头[DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("。AspNetCore.Antiforgery。") 下。 此属性已过时，将在未来版本中删除。 建议的替代项是 Cookie.Name。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | 设置 cookie 的路径。 此属性已过时，将在未来版本中删除。 建议的替代项是 Cookie.Path。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 防伪系统用于呈现防伪令牌在视图中的隐藏的窗体字段的名称。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 防伪系统使用的标头的名称。 如果`null`，系统会认为只有窗体数据。 |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 指定是否由防伪系统需要 HTTPS。 如果`true`，非 HTTPS 发出的请求失败。 默认为 `false`。 此属性已过时，将在未来版本中删除。 建议的替代项是设置 Cookie.SecurePolicy。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否禁止显示生成`X-Frame-Options`标头。 默认情况下，值为"SAMEORIGIN"生成标头。 默认为 `false`。 |

::: moniker-end

有关详细信息，请参阅[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。

## <a name="configure-antiforgery-features-with-iantiforgery"></a>配置与 IAntiforgery 防伪功能

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)提供 API 来配置防伪功能。 `IAntiforgery` 可以在请求`Configure`方法的`Startup`类。 以下示例使用中间件应用程序的主页上生成防伪令牌并将其在响应中发送作为 cookie （使用本主题后面所述的默认 Angular 命名约定）：

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

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)是操作筛选器可应用到单个操作，在控制器或全局范围内。 除非请求包含有效的防伪令牌，将阻止对已应用此筛选器的操作发出的请求。

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

`ValidateAntiForgeryToken`属性需要对操作方法请求修饰，包括 HTTP GET 请求令牌。 如果`ValidateAntiForgeryToken`特性应用于应用程序的控制器，它可以替换`IgnoreAntiforgeryToken`属性。

> [!NOTE]
> ASP.NET Core 不支持自动将 antiforgery 令牌添加到 GET 请求。

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>自动验证防伪令牌仅不安全的 HTTP 方法

ASP.NET Core 应用不生成 antiforgery 令牌进行安全的 HTTP 方法 （GET、 HEAD、 选项和跟踪）。 而不是广泛应用`ValidateAntiForgeryToken`属性，然后将其与重写`IgnoreAntiforgeryToken`属性， [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性可用。 此属性工作原理相同于`ValidateAntiForgeryToken`属性，只不过它不需要使用以下 HTTP 方法发出的请求令牌：

* GET
* HEAD
* 选项
* TRACE

我们建议使用`AutoValidateAntiforgeryToken`广泛用于非 API 方案。 这可确保默认被保护 POST 操作。 替代方法是忽略防伪令牌，默认情况下，除非`ValidateAntiForgeryToken`应用于各个操作方法。 它更有可能在此方案中保留的 POST 操作方法不受保护错误地使应用程序容易受到 CSRF 攻击。 所有Post请求都应发送防伪令牌。

Api 没有发送令牌的非 cookie 一部分的自动机制。 实现可能取决于客户端代码实现。 一些示例如下所示：

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

### <a name="override-global-or-controller-antiforgery-attributes"></a>重写全局或控制器防伪特性

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)使用筛选器不再需要给定操作 （或控制器） 的防伪令牌。 当应用时，此筛选器将重写`ValidateAntiForgeryToken`和`AutoValidateAntiforgeryToken`（全局或控制器上） 在较高级别指定的筛选器。

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

## <a name="refresh-tokens-after-authentication"></a>刷新令牌的身份验证后

用户进行身份验证通过将用户重定向到一个视图或 Razor 页面页后，应刷新令牌。

## <a name="javascript-ajax-and-spas"></a>JavaScript、 AJAX 和 Spa

在传统的基于 HTML 的应用中，防伪令牌将传递给使用隐藏的表单域的服务器。 在基于 JavaScript 的新型应用程序和 Spa 中，以编程方式进行很多请求。 这些 AJAX 请求可以使用其他技术 （如请求标头或 cookie） 来发送令牌。

如果使用 cookie 来存储身份验证令牌，并在服务器上的 API 请求进行身份验证，CSRF 是一个潜在的问题。 如果本地存储用于存储令牌，因为从本地存储的值不会自动发送到每个请求的服务器可能会缓解 CSRF 的漏洞。 因此，使用本地存储来存储客户端和发送令牌作为请求标头是建议的方法上的防伪令牌。

### <a name="javascript"></a>JavaScript

使用 JavaScript 与视图，该令牌可以创建使用从视图中的服务。 注入[Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)到视图并调用服务[GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

此方法无需直接处理从服务器设置 cookie 或从客户端读取它们。

前面的示例中使用 JavaScript 为 POST 的 AJAX 标头中读取的隐藏的字段值。

JavaScript 还可以访问 cookie 中的令牌和 cookie 的内容使用令牌的值创建的标头。

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

假设脚本请求调用的标头中发送令牌`X-CSRF-TOKEN`，配置要寻找的防伪服务`X-CSRF-TOKEN`标头：

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

以下示例使用 JavaScript 来发出 AJAX 请求与相应的标头：

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

AngularJS 使用到地址 CSRF 的约定。 如果服务器发送包含名称的 cookie `XSRF-TOKEN`，AngularJS`$http`服务将 cookie 值添加到标头时它将请求发送到服务器。 此过程是自动的。 标头不需要显式设置在客户端。 标头名称是`X-XSRF-TOKEN`。 服务器应检测此标头，并验证其内容。

ASP.NET Core api 以使用这种规定应用程序启动：

* 将应用配置为提供的令牌在 cookie 中名为`XSRF-TOKEN`。
* 查找名为标头将防伪服务配置`X-XSRF-TOKEN`。

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

[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)类型允许开发人员能够通过往返过程中的每个标记的其他数据来扩展反 CSRF 系统的行为。 [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)每次调用方法生成的字段标记，并返回值嵌入生成的令牌中。 实现器可以返回一个时间戳、 nonce 或任何其他值，然后调用[ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)验证令牌时验证此数据。 客户端的用户名已嵌入在生成的令牌中，因此无需包含此信息。 如果令牌包含补充数据但不是`IAntiForgeryAdditionalDataProvider`是配置，并不验证补充数据。

## <a name="additional-resources"></a>其他资源

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))上[打开 Web 应用程序安全项目](https://www.owasp.org/index.php/Main_Page)(OWASP)。
* <xref:host-and-deploy/web-farm>
