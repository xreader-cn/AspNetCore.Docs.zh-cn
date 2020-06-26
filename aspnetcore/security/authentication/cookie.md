---
title: 在不 ASP.NET Core 的情况下使用 cookie 身份验证Identity
author: rick-anderson
description: 了解如何在不 ASP.NET Core 的情况下使用 cookie 身份验证 Identity 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408820"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="e7489-103">在不 ASP.NET Core 的情况下使用 cookie 身份验证Identity</span><span class="sxs-lookup"><span data-stu-id="e7489-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e7489-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7489-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7489-105">ASP.NET Core Identity 是完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="e7489-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e7489-106">但是，可以使用不带 ASP.NET Core 的基于 cookie 的身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="e7489-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e7489-107">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="e7489-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e7489-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e7489-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7489-109">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="e7489-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e7489-110">使用**电子邮件**地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="e7489-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e7489-111">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs*文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e7489-112">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e7489-113">配置</span><span class="sxs-lookup"><span data-stu-id="e7489-113">Configuration</span></span>

<span data-ttu-id="e7489-114">如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。</span><span class="sxs-lookup"><span data-stu-id="e7489-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e7489-115">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e7489-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e7489-117">`AuthenticationScheme`如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="e7489-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e7489-118">将设置 `AuthenticationScheme` 为[CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 "cookie"。</span><span class="sxs-lookup"><span data-stu-id="e7489-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e7489-119">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="e7489-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e7489-120">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e7489-121">如果没有为 cookie 身份验证方案提供 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它将使用 `CookieAuthenticationDefaults.AuthenticationScheme` （"cookie"）。</span><span class="sxs-lookup"><span data-stu-id="e7489-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e7489-122">默认情况下，身份验证 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e7489-123">当站点访问者未同意数据收集时，允许使用身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e7489-124">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="e7489-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e7489-125">在中 `Startup.Configure` ，调用 `UseAuthentication` 并 `UseAuthorization` 设置 `HttpContext.User` 属性并为请求运行授权中间件。</span><span class="sxs-lookup"><span data-stu-id="e7489-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e7489-126">`UseAuthentication` `UseAuthorization` 在调用之前调用和方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e7489-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="e7489-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e7489-128">在 `CookieAuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="e7489-129">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="e7489-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="e7489-130">[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="e7489-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e7489-131">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="e7489-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e7489-132">使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供给 Cookie 策略中间件来控制 cookie 处理的全局特性，并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。</span><span class="sxs-lookup"><span data-stu-id="e7489-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e7489-133">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="e7489-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e7489-134">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e7489-135">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="e7489-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e7489-136">的 Cookie 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `Cookie.SameSite` ， `CookieAuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="e7489-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e7489-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e7489-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e7489-138">SameSite</span><span class="sxs-lookup"><span data-stu-id="e7489-138">Cookie.SameSite</span></span> | <span data-ttu-id="e7489-139">生成的 SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="e7489-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e7489-140">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-140">SameSiteMode.None</span></span>     | <span data-ttu-id="e7489-141">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-141">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-142">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-143">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-144">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-144">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-145">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-146">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e7489-147">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e7489-148">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-148">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-149">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-150">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-151">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-152">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-153">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e7489-154">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e7489-155">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-155">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-156">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-157">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-158">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e7489-159">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="e7489-160">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="e7489-161">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="e7489-161">Create an authentication cookie</span></span>

<span data-ttu-id="e7489-162">若要创建保存用户信息的 cookie，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="e7489-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e7489-163">将对用户信息进行序列化并将其存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="e7489-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e7489-164"><xref:System.Security.Claims.ClaimsIdentity>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="e7489-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e7489-165">`SignInAsync`创建加密的 cookie，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="e7489-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e7489-166">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e7489-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>默认情况下，仅在几个特定路径上使用，例如登录路径和注销路径。</span><span class="sxs-lookup"><span data-stu-id="e7489-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e7489-168">有关详细信息，请参阅[CookieAuthenticationHandler 源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)。</span><span class="sxs-lookup"><span data-stu-id="e7489-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e7489-169">ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="e7489-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e7489-170">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="e7489-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e7489-171">注销</span><span class="sxs-lookup"><span data-stu-id="e7489-171">Sign out</span></span>

<span data-ttu-id="e7489-172">若要注销当前用户并删除其 cookie，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e7489-173">如果 `CookieAuthenticationDefaults.AuthenticationScheme` （或 "cookie"）未用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e7489-174">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e7489-175">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="e7489-175">React to back-end changes</span></span>

<span data-ttu-id="e7489-176">创建 cookie 后，cookie 就是单一标识源。</span><span class="sxs-lookup"><span data-stu-id="e7489-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e7489-177">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="e7489-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e7489-178">应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。</span><span class="sxs-lookup"><span data-stu-id="e7489-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e7489-179">只要身份验证 cookie 有效，用户就会保持登录到应用。</span><span class="sxs-lookup"><span data-stu-id="e7489-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e7489-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识的验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e7489-181">验证每个请求的 cookie 会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="e7489-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e7489-182">Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="e7489-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e7489-183">如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="e7489-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e7489-184">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="e7489-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e7489-185">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="e7489-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e7489-186">若要在数据库基于值更改时使 cookie 无效 `LastChanged` ，请使用 `LastChanged` 包含数据库中的当前值的声明创建 cookie `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e7489-187">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e7489-188">下面是的示例实现 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e7489-189">在方法中的 cookie 服务注册期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e7489-190">提供类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e7489-191">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="e7489-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e7489-192">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e7489-193">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="e7489-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e7489-194">验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="e7489-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="e7489-195">永久性 cookie</span><span class="sxs-lookup"><span data-stu-id="e7489-195">Persistent cookies</span></span>

<span data-ttu-id="e7489-196">你可能希望 cookie 在浏览器会话之间保持不变。</span><span class="sxs-lookup"><span data-stu-id="e7489-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e7489-197">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="e7489-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e7489-198">下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e7489-199">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="e7489-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e7489-200">如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e7489-201">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="e7489-202">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="e7489-202">Absolute cookie expiration</span></span>

<span data-ttu-id="e7489-203">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="e7489-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e7489-204">若要创建持久性 cookie， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="e7489-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e7489-205">否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="e7489-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e7489-206">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="e7489-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e7489-207">下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="e7489-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e7489-208">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="e7489-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="e7489-209">ASP.NET Core Identity 是完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="e7489-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e7489-210">但是，可以使用不带 ASP.NET Core 的基于 cookie 的身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="e7489-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e7489-211">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="e7489-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e7489-212">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e7489-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7489-213">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="e7489-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e7489-214">使用**电子邮件**地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="e7489-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e7489-215">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs*文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e7489-216">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e7489-217">配置</span><span class="sxs-lookup"><span data-stu-id="e7489-217">Configuration</span></span>

<span data-ttu-id="e7489-218">如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。</span><span class="sxs-lookup"><span data-stu-id="e7489-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e7489-219">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e7489-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e7489-221">`AuthenticationScheme`如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="e7489-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e7489-222">将设置 `AuthenticationScheme` 为[CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 "cookie"。</span><span class="sxs-lookup"><span data-stu-id="e7489-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e7489-223">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="e7489-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e7489-224">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e7489-225">如果没有为 cookie 身份验证方案提供 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它将使用 `CookieAuthenticationDefaults.AuthenticationScheme` （"cookie"）。</span><span class="sxs-lookup"><span data-stu-id="e7489-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e7489-226">默认情况下，身份验证 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e7489-227">当站点访问者未同意数据收集时，允许使用身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e7489-228">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="e7489-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e7489-229">在 `Startup.Configure` 方法中，调用 `UseAuthentication` 方法以调用设置属性的身份验证中间件 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e7489-230">`UseAuthentication`在调用或之前调用 `UseMvcWithDefaultRoute` 方法 `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e7489-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="e7489-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e7489-232">在 `CookieAuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="e7489-233">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="e7489-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="e7489-234">[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="e7489-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e7489-235">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="e7489-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e7489-236">使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供给 Cookie 策略中间件来控制 cookie 处理的全局特性，并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。</span><span class="sxs-lookup"><span data-stu-id="e7489-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e7489-237">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="e7489-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e7489-238">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e7489-239">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="e7489-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e7489-240">的 Cookie 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `Cookie.SameSite` ， `CookieAuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="e7489-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e7489-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e7489-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e7489-242">SameSite</span><span class="sxs-lookup"><span data-stu-id="e7489-242">Cookie.SameSite</span></span> | <span data-ttu-id="e7489-243">生成的 SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="e7489-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e7489-244">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-244">SameSiteMode.None</span></span>     | <span data-ttu-id="e7489-245">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-245">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-246">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-247">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-248">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-248">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-249">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-250">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e7489-251">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e7489-252">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-252">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-253">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-254">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-255">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-256">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-257">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e7489-258">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e7489-259">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-259">SameSiteMode.None</span></span><br><span data-ttu-id="e7489-260">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="e7489-261">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="e7489-262">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="e7489-263">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="e7489-264">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e7489-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="e7489-265">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="e7489-265">Create an authentication cookie</span></span>

<span data-ttu-id="e7489-266">若要创建保存用户信息的 cookie，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="e7489-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e7489-267">将对用户信息进行序列化并将其存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="e7489-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e7489-268"><xref:System.Security.Claims.ClaimsIdentity>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="e7489-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e7489-269">`SignInAsync`创建加密的 cookie，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="e7489-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e7489-270">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e7489-271">ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="e7489-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e7489-272">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="e7489-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e7489-273">注销</span><span class="sxs-lookup"><span data-stu-id="e7489-273">Sign out</span></span>

<span data-ttu-id="e7489-274">若要注销当前用户并删除其 cookie，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e7489-275">如果 `CookieAuthenticationDefaults.AuthenticationScheme` （或 "cookie"）未用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e7489-276">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e7489-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e7489-277">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="e7489-277">React to back-end changes</span></span>

<span data-ttu-id="e7489-278">创建 cookie 后，cookie 就是单一标识源。</span><span class="sxs-lookup"><span data-stu-id="e7489-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e7489-279">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="e7489-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e7489-280">应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。</span><span class="sxs-lookup"><span data-stu-id="e7489-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e7489-281">只要身份验证 cookie 有效，用户就会保持登录到应用。</span><span class="sxs-lookup"><span data-stu-id="e7489-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e7489-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识的验证。</span><span class="sxs-lookup"><span data-stu-id="e7489-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e7489-283">验证每个请求的 cookie 会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="e7489-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e7489-284">Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="e7489-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e7489-285">如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="e7489-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e7489-286">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="e7489-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e7489-287">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="e7489-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e7489-288">若要在数据库基于值更改时使 cookie 无效 `LastChanged` ，请使用 `LastChanged` 包含数据库中的当前值的声明创建 cookie `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e7489-289">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e7489-290">下面是的示例实现 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e7489-291">在方法中的 cookie 服务注册期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e7489-292">提供类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e7489-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e7489-293">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="e7489-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e7489-294">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e7489-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e7489-295">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="e7489-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e7489-296">验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="e7489-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="e7489-297">永久性 cookie</span><span class="sxs-lookup"><span data-stu-id="e7489-297">Persistent cookies</span></span>

<span data-ttu-id="e7489-298">你可能希望 cookie 在浏览器会话之间保持不变。</span><span class="sxs-lookup"><span data-stu-id="e7489-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e7489-299">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="e7489-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e7489-300">下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e7489-301">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="e7489-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e7489-302">如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。</span><span class="sxs-lookup"><span data-stu-id="e7489-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e7489-303">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="e7489-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="e7489-304">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="e7489-304">Absolute cookie expiration</span></span>

<span data-ttu-id="e7489-305">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="e7489-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e7489-306">若要创建持久性 cookie， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="e7489-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e7489-307">否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="e7489-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e7489-308">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="e7489-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e7489-309">下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="e7489-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e7489-310">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="e7489-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e7489-311">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7489-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e7489-312">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="e7489-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
