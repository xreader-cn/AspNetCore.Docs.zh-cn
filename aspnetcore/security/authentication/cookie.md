---
title: 使用 cookie 而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 使用 cookie 而无需 ASP.NET Core 标识的身份验证的说明
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: f3e02b357a83cf5fc4b9fcdc79b2fbe80da98507
ms.sourcegitcommit: 9691b742134563b662948b0ed63f54ef7186801e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2019
ms.locfileid: "66824750"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>使用 cookie 而无需 ASP.NET Core 标识的身份验证

通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)

如你所见在早期的身份验证主题中， [ASP.NET Core 标识](xref:security/authentication/identity)完成、 功能完备的身份验证提供程序是用于创建和维护登录名。 但是，你可能想要使用基于 cookie 的身份验证有时使用您自己的自定义身份验证逻辑。 你可以使用基于 cookie 的身份验证作为独立身份验证提供程序，没有 ASP.NET Core 标识。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）

出于演示目的，示例应用程序中，假设用户、 Maria Rodriguez 的用户帐户是硬编码到应用程序。 使用电子邮件用户名"maria.rodriguez@contoso.com"和任何密码以登录用户。 用户进行身份验证中`AuthenticateUser`中的方法*Pages/Account/Login.cshtml.cs*文件。 在实际示例中，用户会根据数据库身份验证。

有关从 ASP.NET Core 迁移基于 cookie 的身份验证信息 1.x 到 2.0，请参阅[迁移身份验证和标识到 ASP.NET Core 2.0 主题 （基于 Cookie 的身份验证）](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication)。

若要使用 ASP.NET Core 标识，请参阅[标识简介](xref:security/authentication/identity)主题。

## <a name="configuration"></a>配置

::: moniker range=">= aspnetcore-2.0"

如果应用不使用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，在项目文件中创建的包引用[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)包 (版本 2.1.0 或更高版本）。

在中`ConfigureServices`方法中，创建具有的身份验证中间件服务`AddAuthentication`和`AddCookie`方法：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

`AuthenticationScheme` 传递给`AddAuthentication`设置应用程序的默认身份验证方案。 `AuthenticationScheme` 有多个实例的 cookie 身份验证并要时很有用[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。 设置`AuthenticationScheme`到`CookieAuthenticationDefaults.AuthenticationScheme`方案提供的值为"Cookie"。 你可以提供任何字符串值，用于区分方案。

应用程序的身份验证方案是不同的应用程序的 cookie 身份验证方案。 当 cookie 身份验证方案不提供给<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>，它使用[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookie")。

身份验证 cookie<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>属性设置为`true`默认情况下。 网站访问者尚未同意数据收集时允许使用身份验证 cookie。 有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。

在 `Configure` 方法中，使用 `UseAuthentication` 方法调用用于设置 `HttpContext.User` 属性的身份验证中间件。 在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

**AddCookie Options**

[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0)类用于配置身份验证提供程序选项。

| 选项 | 描述 |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | 提供的路径以提供 302 已找到 （URL 重定向） 时触发的`HttpContext.ForbidAsync`。 默认值为 `/Account/AccessDenied`。 |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | 要用于的颁发者[颁发者](/dotnet/api/system.security.claims.claim.issuer)上创建由 cookie 身份验证服务的任何声明的属性。 |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | Cookie 提供服务位置的域名。 默认情况下，这是请求的主机名。 在浏览器仅将 cookie 在请求中发送到匹配的主机名。 您可能希望调整此项是在你的域中有 cookie 提供给任何主机。 例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`，和`staging.www.contoso.com`。 |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | 指示是否 cookie 应只允许到服务器可访问的标志。 此值更改为`false`允许客户端脚本访问 cookie，并可能会打开 cookie 被盗应用应您的应用程序具有[跨站点脚本 (XSS)](xref:security/cross-site-scripting)漏洞。 默认值为 `true`。 |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | 设置的 cookie 的名称。 |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | 用于隔离同一主机名上运行的应用。 如果必须在运行的应用`/app1`并且想要将 cookie 限制为该应用，设置`CookiePath`属性设置为`/app1`。 通过此操作，cookie 是仅可在请求上`/app1`和其下的任何应用。 |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | 指示浏览器是否应允许要附加到同一站点的请求的 cookie (`SameSiteMode.Strict`) 或使用安全的 HTTP 方法和同一站点的请求的跨站点请求 (`SameSiteMode.Lax`)。 如果设置为`SameSiteMode.None`，未设置的 cookie 标头值。 请注意， [Cookie 策略中间件](#cookie-policy-middleware)可能会覆盖你提供的值。 若要支持 OAuth 身份验证，默认值是`SameSiteMode.Lax`。 有关详细信息，请参阅[OAuth 身份验证由于 SameSite cookie 策略中断](https://github.com/aspnet/Security/issues/1231)。 |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | 一个标志，指示是否创建的 cookie 应限制为 HTTPS (`CookieSecurePolicy.Always`)，HTTP 或 HTTPS (`CookieSecurePolicy.None`)，或与请求相同的协议 (`CookieSecurePolicy.SameAsRequest`)。 默认值为 `CookieSecurePolicy.SameAsRequest`。 |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | 集`DataProtectionProvider`用于创建默认`TicketDataFormat`。 如果`TicketDataFormat`属性设置，`DataProtectionProvider`选项不会继续使用。 如果未提供，则使用应用程序的默认数据保护提供程序。 |
| [事件](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | 该处理程序处理的特定点上提供的应用程序控制的提供程序上调用方法。 如果`Events`不提供的默认实例提供时才调用这些方法不执行任何操作。 |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | 用作服务类型，以获取`Events`实例而不是属性。 |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | `TimeSpan`后将存储在 cookie 的身份验证票证到期。 `ExpireTimeSpan` 添加到当前时间来创建票证的到期时间。 `ExpiredTimeSpan`值始终会进入加密 AuthTicket 验证服务器。 此外可能进入[Set-cookie](https://tools.ietf.org/html/rfc6265#section-4.1)标头，但仅当`IsPersistent`设置。 若要设置`IsPersistent`到`true`，配置[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties)传递给`SignInAsync`。 默认值`ExpireTimeSpan`为 14 天。 |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | 提供的路径以提供 302 已找到 （URL 重定向） 时触发的`HttpContext.ChallengeAsync`。 生成 401 的当前 URL 添加到`LoginPath`作为查询字符串参数的名为`ReturnUrlParameter`。 一次请求`LoginPath`授予新登录标识，`ReturnUrlParameter`值用于将浏览器重定向回导致原始未授权的状态代码的 URL。 默认值为 `/Account/Login`。 |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | 如果`LogoutPath`提供给处理程序，则将重定向到该路径的请求值的基础`ReturnUrlParameter`。 默认值为 `/Account/Logout`。 |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | 确定由 302 已找到 （URL 重定向） 响应的处理程序追加查询字符串参数的名称。 `ReturnUrlParameter` 请求到达时，将使用`LoginPath`或`LogoutPath`来执行登录或注销操作后返回到原始 URL 的浏览器。 默认值为 `ReturnUrl`。 |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | 一个可选容器，用于标识存储在请求之间。 使用时，只将会话标识符发送到客户端。 `SessionStore` 可以用于缓解大型标识的潜在问题。 |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | 指示是否应动态颁发包含更新的到期时间的新 cookie 的标志。 这可以在任何请求，其中当前 cookie 过期时间已超过 50%过期。 向前移动新的到期日期为当前日期加上`ExpireTimespan`。 [绝对 cookie 到期时间](xref:security/authentication/cookie#absolute-cookie-expiration)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。 绝对过期时间可以通过限制身份验证 cookie 是有效的时间量来提高您的应用程序的安全性。 默认值为 `true`。 |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | `TicketDataFormat`用于保护和取消保护标识和存储在 cookie 值中的其他属性。 如果未提供，`TicketDataFormat`使用创建[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0)。 |
| [验证](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | 检查的选项是有效的方法。 |

设置`CookieAuthenticationOptions`中的身份验证的服务配置中`ConfigureServices`方法：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

ASP.NET Core 1.x 使用 cookie[中间件](xref:fundamentals/middleware/index)，序列化为一个用户主体的加密 cookie。 在后续请求中，对 cookie 进行验证，并重新创建和分配给主体`HttpContext.User`属性。

安装[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)在项目中的 NuGet 包。 此包包含 cookie 中间件。

使用`UseCookieAuthentication`中的方法`Configure`中的方法您*Startup.cs*文件，然后`UseMvc`或`UseMvcWithDefaultRoute`:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

**CookieAuthenticationOptions 选项**

[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1)类用于配置身份验证提供程序选项。

| 选项 | 描述 |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | 设置身份验证方案。 `AuthenticationScheme` 时，可以有多个实例的身份验证，并且你想要[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。 设置`AuthenticationScheme`到`CookieAuthenticationDefaults.AuthenticationScheme`方案提供的值为"Cookie"。 你可以提供任何字符串值，用于区分方案。 |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | 设置一个值，以指示 cookie 身份验证应在每个请求上运行并尝试验证并重新构造它创建的任何序列化的主体。 |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | 如果为 true，则身份验证中间件处理自动挑战。 如果为 false，身份验证中间件仅更改时进行显式指定响应`AuthenticationScheme`。 |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | 要用于的颁发者[颁发者](/dotnet/api/system.security.claims.claim.issuer)上创建的 cookie 身份验证中间件的任何声明的属性。 |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | Cookie 提供服务位置的域名。 默认情况下，这是请求的主机名。 在浏览器只起到匹配的主机名的 cookie。 您可能希望调整此项是在你的域中有 cookie 提供给任何主机。 例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`，和`staging.www.contoso.com`。 |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | 指示是否 cookie 应只允许到服务器可访问的标志。 此值更改为`false`允许客户端脚本访问 cookie，并可能会打开 cookie 被盗应用应您的应用程序具有[跨站点脚本 (XSS)](xref:security/cross-site-scripting)漏洞。 默认值为 `true`。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | 用于隔离同一主机名上运行的应用。 如果必须在运行的应用`/app1`并且想要将 cookie 限制为该应用，设置`CookiePath`属性设置为`/app1`。 通过此操作，cookie 是仅可在请求上`/app1`和其下的任何应用。 |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | 一个标志，指示是否创建的 cookie 应限制为 HTTPS (`CookieSecurePolicy.Always`)，HTTP 或 HTTPS (`CookieSecurePolicy.None`)，或与请求相同的协议 (`CookieSecurePolicy.SameAsRequest`)。 默认值为 `CookieSecurePolicy.SameAsRequest`。 |
| [说明](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | 有关提供给应用程序的身份验证类型的其他信息。 |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | `TimeSpan`后将身份验证票证到期。 它被添加到当前时间来创建票证的到期时间。 若要使用`ExpireTimeSpan`，则必须设置`IsPersistent`到`true`中`AuthenticationProperties`传递给`SignInAsync`。 默认值为 14 天。 |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | 一个标志，指示 cookie 的到期日期重置多个的下半部分`ExpireTimeSpan`经过一段时间。 向前移动新的过期时间为当前日期加上`ExpireTimespan`。 [绝对 cookie 到期时间](xref:security/authentication/cookie#absolute-cookie-expiration)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。 绝对过期时间可以通过限制身份验证 cookie 是有效的时间量来提高您的应用程序的安全性。 默认值为 `true`。 |

设置`CookieAuthenticationOptions`Cookie 身份验证中间件中`Configure`方法：

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a>Cookie 策略中间件

[Cookie 策略中间件](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware)使应用程序中的 cookie 策略功能。 到应用程序处理管道添加中间件是顺序敏感;它只影响在管道中注册后的组件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)提供给 Cookie 策略中间件可用于控制时追加或删除 cookie 的 cookie 处理和挂钩全局特性 cookie 处理程序。

| 属性 | 描述 |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | 会影响是否 cookie 必须 HttpOnly，该值一个标志，指示是否 cookie 应只允许到服务器可访问。 默认值为 `HttpOnlyPolicy.None`。 |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | 影响的 cookie 的同一站点属性 （见下文）。 默认值为 `SameSiteMode.Lax`。 此选项仅供 ASP.NET Core 2.0 +。 |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | 当会追加一个 cookie 时调用。 |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | 当删除 cookie 时调用。 |
| [Secure](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | 会影响是否 cookie 必须是安全。 默认值为 `CookieSecurePolicy.None`。 |

**MinimumSameSitePolicy** (ASP.NET Core 2.0 + 仅)

默认值`MinimumSameSitePolicy`值是`SameSiteMode.Lax`允许 OAuth2 身份验证。 若要严格强制实施的同一站点策略`SameSiteMode.Strict`，请将`MinimumSameSitePolicy`。 尽管此设置会中断的 OAuth2 和其他跨域身份验证方案，它会提升为其他类型的应用不依赖于跨域请求处理的 cookie 安全级别。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Cookie 策略中间件设置`MinimumSameSitePolicy`可能会影响您的设置的`Cookie.SameSite`中`CookieAuthenticationOptions`根据下面的表格的设置。

| MinimumSameSitePolicy | Cookie.SameSite | 最终的 Cookie.SameSite 设置 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>创建身份验证 cookie

若要创建保存用户信息的 cookie，必须构造[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)。 用户信息序列化并存储在 cookie 中。 

::: moniker range=">= aspnetcore-2.0"

创建[ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity)包含任何必需[声明](/dotnet/api/system.security.claims.claim)s 和调用[SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0)以登录用户：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

调用[SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1)以登录用户：

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

`SignInAsync` 创建一个加密的 cookie，并将其添加到当前响应。 如果未指定`AuthenticationScheme`，则使用默认方案。

事实上，使用的加密是 ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection)系统。 如果托管应用在多台计算机、 负载平衡跨应用程序，或使用 web 场，则必须[配置数据保护](xref:security/data-protection/configuration/overview)使用相同密钥环和应用程序标识符。

## <a name="sign-out"></a>注销

::: moniker range=">= aspnetcore-2.0"

若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

如果不使用`CookieAuthenticationDefaults.AuthenticationScheme`（或"Cookie"） 作为方案 (例如，"ContosoCookie")，提供配置身份验证提供程序时使用的方案。 否则，使用默认方案。

## <a name="react-to-back-end-changes"></a>对后端更改做出响应

一旦创建了 cookie，它将成为标识的单个来源。 即使在后端系统中禁用用户，cookie 身份验证系统不了解，并且用户保持登录状态，只要其 cookie 有效。

[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal)中 ASP.NET Core 事件 2.x 或[ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1)中 ASP.NET Core 1.x 可用来截获和重写的 cookie 身份验证方法。 此方法可减轻已吊销的用户访问应用的风险。

Cookie 验证的一种方法取决于跟踪的更改用户数据库时。 如果数据库未已发生更改，因为发布用户的 cookie，则无需重新验证用户，如果其 cookie 仍然有效。 若要实现此方案中，数据库中实现`IUserRepository`对于此示例中，存储`LastChanged`值。 在数据库中，更新的任何用户时`LastChanged`值设置为当前时间。

为了使 cookie 无效时的数据库更改基于`LastChanged`值时，请创建与 cookie`LastChanged`包含当前声明`LastChanged`值位于数据库中：

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker range=">= aspnetcore-2.0"

若要实现的替代`ValidatePrincipal`事件，编写一个方法具有以下签名在从派生类中[CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

示例如下所示：

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

在中的 cookie 服务注册期间注册的事件实例`ConfigureServices`方法。 提供的作用域内的服务注册你`CustomCookieAuthenticationEvents`类：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要实现的替代`ValidateAsync`事件，编写一个方法具有以下签名：

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

ASP.NET Core 标识作为的一部分实现这一检查其[SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync)。 示例如下所示：

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

在中的 cookie 身份验证配置期间注册事件`Configure`方法：

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

请考虑在其中更新用户的名称的情况下&mdash;不会影响任何方式的安全决策。 如果您需要非破坏性地更新用户主体，请调用`context.ReplacePrincipal`并设置`context.ShouldRenew`属性设置为`true`。

> [!WARNING]
> 此处所述的方法上的每个请求触发。 这可能导致应用程序对较大性能产生负面影响。

## <a name="persistent-cookies"></a>永久 cookie

你可能想要在浏览器会话之间持久保存的 cookie。 仅进行显式用户同意，有一个"记住我"复选框上登录名或类似机制，应启用此持久性。 

下面的代码段创建的标识和相应可以幸存，但通过浏览器闭包的 cookie。 遵循以前配置任何滑动过期设置。 如果 cookie 已过期浏览器处于关闭状态时，浏览器中清除 cookie 后重新启动它。

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0)类驻留在`Microsoft.AspNetCore.Authentication`命名空间。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1)类驻留在`Microsoft.AspNetCore.Http.Authentication`命名空间。

::: moniker-end

## <a name="absolute-cookie-expiration"></a>绝对 cookie 到期时间

可以设置使用绝对到期时间`ExpiresUtc`。 若要创建持久 cookie，则还必须设置`IsPersistent`; 否则为 cookie 使用基于会话的生存期内创建，并且可能会到期之前或身份验证票证后它将保持。 当`ExpiresUtc`上设置`SignInAsync`，它将重写的值`ExpireTimeSpan`的选项`CookieAuthenticationOptions`，如果设置。

下面的代码段创建的标识和相应的 cookie 的持续时间为 20 分钟。 这会忽略以前配置的任何滑动过期设置。

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [身份验证 2.0 更改 / 迁移公告](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [基于策略的角色检查](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
