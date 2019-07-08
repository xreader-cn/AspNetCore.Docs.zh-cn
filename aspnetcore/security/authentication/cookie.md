---
title: 使用 cookie 而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 了解如何使用 cookie 身份验证，而无需 ASP.NET Core 标识。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622747"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>使用 cookie 而无需 ASP.NET Core 标识的身份验证

通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)

ASP.NET Core 标识用于创建和维护的登录名是完整的全功能的身份验证提供程序。 但是，可以使用基于 cookie 的身份验证身份验证提供程序，没有 ASP.NET Core 标识。 有关详细信息，请参阅 <xref:security/authentication/identity>。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）

出于演示目的，示例应用程序中，假设用户、 Maria Rodriguez 的用户帐户是硬编码到应用程序。 使用**电子邮件**用户名`maria.rodriguez@contoso.com`和任何密码以登录用户。 用户进行身份验证中`AuthenticateUser`中的方法*Pages/Account/Login.cshtml.cs*文件。 在实际示例中，用户会根据数据库身份验证。

## <a name="configuration"></a>配置

如果应用不使用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，在项目文件中创建的包引用[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)包。

在中`Startup.ConfigureServices`方法中，创建具有的身份验证中间件服务<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>和<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>方法：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递给`AddAuthentication`设置应用程序的默认身份验证方案。 `AuthenticationScheme` 有多个实例的 cookie 身份验证并要时很有用[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。 设置`AuthenticationScheme`到[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)方案提供的值为"Cookie"。 你可以提供任何字符串值，用于区分方案。

应用程序的身份验证方案是不同的应用程序的 cookie 身份验证方案。 当 cookie 身份验证方案不提供给<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>，它使用`CookieAuthenticationDefaults.AuthenticationScheme`("Cookie")。

身份验证 cookie<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>属性设置为`true`默认情况下。 网站访问者尚未同意数据收集时允许使用身份验证 cookie。 有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。

在中`Startup.Configure`方法中，调用`UseAuthentication`方法调用设置身份验证中间件`HttpContext.User`属性。 在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。

设置`CookieAuthenticationOptions`中的身份验证的服务配置中`Startup.ConfigureServices`方法：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie 策略中间件

[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)使 cookie 策略功能。 到应用程序处理管道添加中间件是敏感的顺序&mdash;它只影响在管道中注册的下游组件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>提供给 Cookie 策略中间件时追加或删除 cookie 的 cookie 处理和挂钩全局特性控制到 cookie 处理程序。

默认值<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>值是`SameSiteMode.Lax`允许 OAuth2 身份验证。 若要严格强制实施的同一站点策略`SameSiteMode.Strict`，请将`MinimumSameSitePolicy`。 尽管此设置会中断的 OAuth2 和其他跨域身份验证方案，它会提升为其他类型的应用不依赖于跨域请求处理的 cookie 安全级别。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Cookie 策略中间件设置`MinimumSameSitePolicy`可能会影响的设置`Cookie.SameSite`中`CookieAuthenticationOptions`根据下面的表格的设置。

| MinimumSameSitePolicy | Cookie.SameSite | 最终的 Cookie.SameSite 设置 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>创建身份验证 cookie

若要创建 cookie 保存用户的信息，请构造<xref:System.Security.Claims.ClaimsPrincipal>。 用户信息序列化并存储在 cookie 中。 

创建<xref:System.Security.Claims.ClaimsIdentity>与任何所需<xref:System.Security.Claims.Claim>s 和调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>以登录用户：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` 创建一个加密的 cookie，并将其添加到当前响应。 如果`AuthenticationScheme`未指定，则使用默认方案。

ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。 对于多台计算机上托管的应用，负载平衡应用程序，或使用 web 场[配置数据保护](xref:security/data-protection/configuration/overview)使用相同密钥环和应用程序标识符。

## <a name="sign-out"></a>注销

若要注销当前用户，然后删除其 cookie，调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果`CookieAuthenticationDefaults.AuthenticationScheme`（或"Cookie"） 未使用的方案 (例如，"ContosoCookie")，提供配置身份验证提供程序时所使用的方案。 否则，使用默认方案。

## <a name="react-to-back-end-changes"></a>对后端更改做出响应

一旦创建了 cookie，cookie 是标识的单个来源。 如果在后端系统中禁用用户帐户：

* 应用程序的 cookie 身份验证系统将继续处理基于身份验证 cookie 的请求。
* 只要是有效的身份验证 cookie，用户就会保持登录到应用程序。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写的 cookie 身份验证。 验证对每个请求的 cookie 应吊销用户访问应用的风险。

Cookie 验证的一种方法取决于跟踪的用户数据库发生更改时。 如果数据库未已发生更改，因为发布用户的 cookie，则无需重新验证用户，如果其 cookie 仍然有效。 在示例应用中，在数据库中实现`IUserRepository`，并将存储`LastChanged`值。 在数据库中，更新用户时`LastChanged`值设置为当前时间。

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

若要实现的替代`ValidatePrincipal`事件，编写一个方法具有以下签名在派生类中<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

以下是示例实现`CookieAuthenticationEvents`:

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

在中的 cookie 服务注册期间注册的事件实例`Startup.ConfigureServices`方法。 提供[范围内服务注册](xref:fundamentals/dependency-injection#service-lifetimes)为你`CustomCookieAuthenticationEvents`类：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

请考虑在其中更新用户的名称的情况下&mdash;不会影响任何方式的安全决策。 如果您需要非破坏性地更新用户主体，请调用`context.ReplacePrincipal`并设置`context.ShouldRenew`属性设置为`true`。

> [!WARNING]
> 此处所述的方法上的每个请求触发。 验证每个请求上的所有用户的身份验证 cookie 可能会导致应用程序对较大性能产生负面影响。

## <a name="persistent-cookies"></a>永久 cookie

你可能想要在浏览器会话之间持久保存的 cookie。 此持久性仅应启用"记住我"复选框在登录或类似机制显式用户同意。 

下面的代码段创建的标识和相应可以幸存，但通过浏览器闭包的 cookie。 遵循以前配置任何滑动过期设置。 如果 cookie 已过期浏览器处于关闭状态时，浏览器中清除 cookie 后重新启动它。

设置<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>到`true`中<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>绝对 cookie 到期时间

可使用设置绝对过期时间<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>。 若要创建持久 cookie`IsPersistent`还必须设置。 否则为 cookie 使用基于会话的生存期内创建的并且可能过期之前或之后它所持有的身份验证票证。 当`ExpiresUtc`，则它将重写的值<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>的选项<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>，如果设置。

下面的代码段创建的标识和相应的 cookie 的持续时间为 20 分钟。 这会忽略以前配置的任何滑动过期设置。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [基于策略的角色检查](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
