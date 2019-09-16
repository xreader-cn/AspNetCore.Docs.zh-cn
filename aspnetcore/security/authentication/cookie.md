---
title: 使用 cookie 而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 了解如何使用 cookie 身份验证，而无需 ASP.NET Core 标识。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 76c7fc20c8870668ca7c65d975e2ed59f40f7dc8
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384829"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>使用 cookie 而无需 ASP.NET Core 标识的身份验证

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 标识是完整、功能齐全的身份验证提供程序，用于创建和维护登录名。 但是，可以使用不带 ASP.NET Core 标识的基于 cookie 的身份验证提供程序。 有关详细信息，请参阅 <xref:security/authentication/identity> 。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）

出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。 使用**电子邮件**地址`maria.rodriguez@contoso.com`和任何密码来登录用户。 用户通过`AuthenticateUser` *页面/帐户/登录名. .cs*文件中的方法进行身份验证。 在实际的示例中，用户将对数据库进行身份验证。

## <a name="configuration"></a>配置

如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。

在方法中，创建<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>具有和<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>方法的身份验证中间件服务： `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>传递以`AddAuthentication`设置应用程序的默认身份验证方案。 `AuthenticationScheme`如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。 将`AuthenticationScheme`设置为 [CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 为方案提供值 "cookie"。 可以提供任何用于区分方案的字符串值。

应用的身份验证方案不同于应用的 cookie 身份验证方案。 如果没有为<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>cookie 身份验证方案提供，它将`CookieAuthenticationDefaults.AuthenticationScheme`使用（"cookie"）。

默认情况下， <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>身份验证 cookie 的`true`属性设置为。 当站点访问者未同意数据收集时，允许使用身份验证 cookie。 有关详细信息，请参阅 <xref:security/gdpr#essential-cookies> 。

在`Startup.Configure`中， `UseAuthentication`调用`UseAuthorization`并设置`HttpContext.User`属性并为请求运行授权中间件。 在调用`UseAuthorization` `UseAuthentication` 之前`UseEndpoints`调用和方法：

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。

在`CookieAuthenticationOptions`服务配置中设置，以便`Startup.ConfigureServices`在方法中进行身份验证：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie 策略中间件

[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。 将中间件添加到应用处理管道是区分&mdash;顺序的，它仅影响在管道中注册的下游组件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>提供给 cookie 策略中间件来控制 cookie 处理的全局特性，并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 默认`SameSiteMode.Lax`值为允许 OAuth2 authentication。 若要严格地强制执行同一站点策略`SameSiteMode.Strict`，请`MinimumSameSitePolicy`设置。 尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 策略中间件设置`MinimumSameSitePolicy`会影响`Cookie.SameSite`中`CookieAuthenticationOptions`的设置，具体取决于下面的矩阵。

| MinimumSameSitePolicy | Cookie.SameSite | 生成的 SameSite 设置 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>创建身份验证 cookie

若要创建保存用户信息的 cookie，请<xref:System.Security.Claims.ClaimsPrincipal>构造。 将对用户信息进行序列化并将其存储在 cookie 中。 

使用任何所需<xref:System.Security.Claims.Claim>的和调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>来登录用户： <xref:System.Security.Claims.ClaimsIdentity>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`创建加密的 cookie，并将其添加到当前响应中。 如果`AuthenticationScheme`未指定，则使用默认方案。

ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。 对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>：

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果`CookieAuthenticationDefaults.AuthenticationScheme` （或 "cookie"）未用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。 否则，将使用默认方案。

## <a name="react-to-back-end-changes"></a>对后端更改作出反应

创建 cookie 后，cookie 就是单一标识源。 如果在后端系统中禁用用户帐户：

* 应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。
* 只要身份验证 cookie 有效，用户就会保持登录到应用。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识的验证。 验证每个请求的 cookie 会降低吊销用户访问应用的风险。

Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。 如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。 在示例应用中，数据库在中`IUserRepository`实现并存储一个`LastChanged`值。 当用户在数据库中更新时， `LastChanged`该值将设置为当前时间。

若要在数据库基于`LastChanged`值更改时使 cookie 无效，请`LastChanged`使用包含数据库中的当前`LastChanged`值的声明创建 cookie：

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

若要为`ValidatePrincipal`事件实现替代，请在派生自<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>的类中编写具有以下签名的方法：

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

下面是的`CookieAuthenticationEvents`示例实现：

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

在`Startup.ConfigureServices`方法中的 cookie 服务注册期间注册事件实例。 提供`CustomCookieAuthenticationEvents`类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes)：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

请考虑这样一种情况：用户的名称更新&mdash;为不影响安全的决策。 如果要以非破坏性的更新用户主体，请调用`context.ReplacePrincipal` ，并`context.ShouldRenew`将属性设置`true`为。

> [!WARNING]
> 此处所述的方法在每个请求时触发。 验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。

## <a name="persistent-cookies"></a>永久性 cookie

你可能希望 cookie 在浏览器会话之间保持不变。 仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。 

下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。 预先配置的任何可调过期设置都将起作用。 如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。

设置<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>为`true` in ：<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

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

## <a name="absolute-cookie-expiration"></a>绝对 cookie 过期

绝对过期时间可以设置为<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>。 若要创建持久性 cookie， `IsPersistent`还必须设置。 否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。 设置`ExpiresUtc`后，它将覆盖的<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>选项的<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>值（如果已设置）。

下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。 这将忽略以前配置的任何可调过期设置。

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 标识是完整、功能齐全的身份验证提供程序，用于创建和维护登录名。 但是，可以使用不带 ASP.NET Core 标识的基于 cookie 的身份验证提供程序。 有关详细信息，请参阅 <xref:security/authentication/identity> 。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）

出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。 使用**电子邮件**地址`maria.rodriguez@contoso.com`和任何密码来登录用户。 用户通过`AuthenticateUser` *页面/帐户/登录名. .cs*文件中的方法进行身份验证。 在实际的示例中，用户将对数据库进行身份验证。

## <a name="configuration"></a>配置

如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。

在方法中，创建<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>具有和<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>方法的身份验证中间件服务： `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>传递以`AddAuthentication`设置应用程序的默认身份验证方案。 `AuthenticationScheme`如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。 将`AuthenticationScheme`设置为 [CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 为方案提供值 "cookie"。 可以提供任何用于区分方案的字符串值。

应用的身份验证方案不同于应用的 cookie 身份验证方案。 如果没有为<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>cookie 身份验证方案提供，它将`CookieAuthenticationDefaults.AuthenticationScheme`使用（"cookie"）。

默认情况下， <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>身份验证 cookie 的`true`属性设置为。 当站点访问者未同意数据收集时，允许使用身份验证 cookie。 有关详细信息，请参阅 <xref:security/gdpr#essential-cookies> 。

在方法中， `UseAuthentication`调用方法以调用设置`HttpContext.User`属性的身份验证中间件。 `Startup.Configure` 在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。

在`CookieAuthenticationOptions`服务配置中设置，以便`Startup.ConfigureServices`在方法中进行身份验证：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie 策略中间件

[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。 将中间件添加到应用处理管道是区分&mdash;顺序的，它仅影响在管道中注册的下游组件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>提供给 cookie 策略中间件来控制 cookie 处理的全局特性，并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 默认`SameSiteMode.Lax`值为允许 OAuth2 authentication。 若要严格地强制执行同一站点策略`SameSiteMode.Strict`，请`MinimumSameSitePolicy`设置。 尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 策略中间件设置`MinimumSameSitePolicy`会影响`Cookie.SameSite`中`CookieAuthenticationOptions`的设置，具体取决于下面的矩阵。

| MinimumSameSitePolicy | Cookie.SameSite | 生成的 SameSite 设置 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>创建身份验证 cookie

若要创建保存用户信息的 cookie，请<xref:System.Security.Claims.ClaimsPrincipal>构造。 将对用户信息进行序列化并将其存储在 cookie 中。 

使用任何所需<xref:System.Security.Claims.Claim>的和调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>来登录用户： <xref:System.Security.Claims.ClaimsIdentity>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`创建加密的 cookie，并将其添加到当前响应中。 如果`AuthenticationScheme`未指定，则使用默认方案。

ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。 对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果`CookieAuthenticationDefaults.AuthenticationScheme` （或 "cookie"）未用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。 否则，将使用默认方案。

## <a name="react-to-back-end-changes"></a>对后端更改作出反应

创建 cookie 后，cookie 就是单一标识源。 如果在后端系统中禁用用户帐户：

* 应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。
* 只要身份验证 cookie 有效，用户就会保持登录到应用。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识的验证。 验证每个请求的 cookie 会降低吊销用户访问应用的风险。

Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。 如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。 在示例应用中，数据库在中`IUserRepository`实现并存储一个`LastChanged`值。 当用户在数据库中更新时， `LastChanged`该值将设置为当前时间。

若要在数据库基于`LastChanged`值更改时使 cookie 无效，请`LastChanged`使用包含数据库中的当前`LastChanged`值的声明创建 cookie：

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

若要为`ValidatePrincipal`事件实现替代，请在派生自<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>的类中编写具有以下签名的方法：

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

下面是的`CookieAuthenticationEvents`示例实现：

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

在`Startup.ConfigureServices`方法中的 cookie 服务注册期间注册事件实例。 提供`CustomCookieAuthenticationEvents`类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes)：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

请考虑这样一种情况：用户的名称更新&mdash;为不影响安全的决策。 如果要以非破坏性的更新用户主体，请调用`context.ReplacePrincipal` ，并`context.ShouldRenew`将属性设置`true`为。

> [!WARNING]
> 此处所述的方法在每个请求时触发。 验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。

## <a name="persistent-cookies"></a>永久性 cookie

你可能希望 cookie 在浏览器会话之间保持不变。 仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。 

下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。 预先配置的任何可调过期设置都将起作用。 如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。

设置<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>为`true` in ：<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

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

## <a name="absolute-cookie-expiration"></a>绝对 cookie 过期

绝对过期时间可以设置为<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>。 若要创建持久性 cookie， `IsPersistent`还必须设置。 否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。 设置`ExpiresUtc`后，它将覆盖的<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>选项的<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>值（如果已设置）。

下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。 这将忽略以前配置的任何可调过期设置。

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

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [基于策略的角色检查](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
