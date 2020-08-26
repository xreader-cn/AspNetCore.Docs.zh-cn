---
title: cookie不使用身份验证ASP.NET Core Identity
author: rick-anderson
description: 了解如何在 cookie 不使用身份验证的情况下使用 ASP.NET Core Identity 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
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
uid: security/authentication/cookie
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876758"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="e62c6-103">cookie不使用身份验证ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e62c6-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e62c6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e62c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e62c6-105">ASP.NET Core Identity 是一个完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="e62c6-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e62c6-106">但是， cookie 不能使用基于的身份验证提供程序 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e62c6-107">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="e62c6-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e62c6-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e62c6-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e62c6-109">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="e62c6-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e62c6-110">使用 **电子邮件** 地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="e62c6-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e62c6-111">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs* 文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e62c6-112">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e62c6-113">配置</span><span class="sxs-lookup"><span data-stu-id="e62c6-113">Configuration</span></span>

<span data-ttu-id="e62c6-114">如果应用程序不使用 [AspNetCore 元包](xref:fundamentals/metapackage-app)，请在项目文件中为 AspNetCore 创建包引用 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 包。</span><span class="sxs-lookup"><span data-stu-id="e62c6-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e62c6-115">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e62c6-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e62c6-117">`AuthenticationScheme` 如果有多个 cookie 身份验证实例，并且你想要 [使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="e62c6-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e62c6-118">将设置 `AuthenticationScheme` 为[ Cookie AuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 " Cookie s"。</span><span class="sxs-lookup"><span data-stu-id="e62c6-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e62c6-119">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="e62c6-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e62c6-120">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e62c6-121">当 cookie 未提供身份验证方案时 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它将使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e62c6-122">cookie默认情况下，身份验证的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e62c6-123">cookie当站点访问者未同意数据收集时，允许使用身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e62c6-124">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="e62c6-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e62c6-125">在中 `Startup.Configure` ，调用 `UseAuthentication` 并 `UseAuthorization` 设置 `HttpContext.User` 属性并为请求运行授权中间件。</span><span class="sxs-lookup"><span data-stu-id="e62c6-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e62c6-126">`UseAuthentication` `UseAuthorization` 在调用之前调用和方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e62c6-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="e62c6-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e62c6-128">在 `CookieAuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e62c6-129">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="e62c6-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="e62c6-130">[ Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)启用 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="e62c6-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e62c6-131">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="e62c6-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e62c6-132">使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供给 Cookie 策略中间件来控制 cookie cookie 在 cookie 追加或删除时处理和挂钩处理处理程序的全局特性。</span><span class="sxs-lookup"><span data-stu-id="e62c6-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e62c6-133">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="e62c6-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e62c6-134">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e62c6-135">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它会提升 cookie 其他不依赖于跨源请求处理的应用类型的安全级别。</span><span class="sxs-lookup"><span data-stu-id="e62c6-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e62c6-136">的 Cookie 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `Cookie.SameSite` ， `CookieAuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="e62c6-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e62c6-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e62c6-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e62c6-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="e62c6-138">Cookie.SameSite</span></span> | <span data-ttu-id="e62c6-139">结果 Cookie 。SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="e62c6-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e62c6-140">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-140">SameSiteMode.None</span></span>     | <span data-ttu-id="e62c6-141">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-141">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-142">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-143">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-144">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-144">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-145">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-146">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e62c6-147">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e62c6-148">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-148">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-149">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-150">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-151">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-152">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-153">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e62c6-154">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e62c6-155">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-155">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-156">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-157">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-158">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e62c6-159">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="e62c6-160">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e62c6-161">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="e62c6-161">Create an authentication cookie</span></span>

<span data-ttu-id="e62c6-162">若要创建 cookie 保存用户信息，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e62c6-163">将序列化用户信息并将其存储在中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e62c6-164"><xref:System.Security.Claims.ClaimsIdentity>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="e62c6-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e62c6-165">`SignInAsync` 创建一个加密的 cookie ，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="e62c6-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e62c6-166">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e62c6-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 默认情况下，仅在几个特定路径上使用，例如登录路径和注销路径。</span><span class="sxs-lookup"><span data-stu-id="e62c6-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e62c6-168">有关详细信息，请参阅[ Cookie AuthenticationHandler 源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)。</span><span class="sxs-lookup"><span data-stu-id="e62c6-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e62c6-169">ASP.NET Core 的 [数据保护](xref:security/data-protection/using-data-protection) 系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="e62c6-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e62c6-170">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请 [将数据保护配置](xref:security/data-protection/configuration/overview) 为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="e62c6-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e62c6-171">注销</span><span class="sxs-lookup"><span data-stu-id="e62c6-171">Sign out</span></span>

<span data-ttu-id="e62c6-172">若要注销当前用户并将其删除 cookie ，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e62c6-173">如果 `CookieAuthenticationDefaults.AuthenticationScheme` (或 " Cookie s" ) 未用作方案 (例如 "Contoso Cookie " ) ，请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e62c6-174">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="e62c6-175">服务器不能控制客户端浏览器。</span><span class="sxs-lookup"><span data-stu-id="e62c6-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="e62c6-176">如果用户关闭浏览器或选项卡，服务器将无法注销用户。</span><span class="sxs-lookup"><span data-stu-id="e62c6-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="e62c6-177">若要在浏览器关闭时实现用户注销，必须通过 JavaScript 检测该问题。</span><span class="sxs-lookup"><span data-stu-id="e62c6-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="e62c6-178">搜索 "如何检测浏览器窗口选项卡关闭事件？"。</span><span class="sxs-lookup"><span data-stu-id="e62c6-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e62c6-179">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="e62c6-179">React to back-end changes</span></span>

<span data-ttu-id="e62c6-180">一旦 cookie 创建， cookie 就是标识的单个源。</span><span class="sxs-lookup"><span data-stu-id="e62c6-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e62c6-181">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="e62c6-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e62c6-182">应用的 cookie 身份验证系统将继续根据身份验证处理请求 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e62c6-183">只要身份验证有效，用户就会保持登录到应用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e62c6-184"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e62c6-185">验证 cookie 每个请求是否会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="e62c6-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e62c6-186">验证的一种方法 cookie 是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="e62c6-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e62c6-187">如果自用户颁发以来数据库尚未发生更改 cookie ，则无需重新对用户进行身份验证，前提 cookie 是用户仍有效。</span><span class="sxs-lookup"><span data-stu-id="e62c6-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e62c6-188">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="e62c6-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e62c6-189">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="e62c6-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e62c6-190">为了使 cookie 数据库根据值发生更改时失效 `LastChanged` ，请 cookie 使用 `LastChanged` 包含数据库中的当前值的声明创建 `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e62c6-191">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e62c6-192">下面是的示例实现 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e62c6-193">cookie在方法中注册服务期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e62c6-194">提供类的 [作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e62c6-195">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="e62c6-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e62c6-196">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e62c6-197">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="e62c6-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e62c6-198">验证 cookie 每个请求的所有用户的身份验证可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="e62c6-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e62c6-199">持久性 cookie</span><span class="sxs-lookup"><span data-stu-id="e62c6-199">Persistent cookies</span></span>

<span data-ttu-id="e62c6-200">你可能希望在 cookie 浏览器会话之间保持。</span><span class="sxs-lookup"><span data-stu-id="e62c6-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e62c6-201">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="e62c6-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e62c6-202">下面的代码片段将创建一个标识，并 cookie 在浏览器闭包中置对应。</span><span class="sxs-lookup"><span data-stu-id="e62c6-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e62c6-203">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="e62c6-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e62c6-204">如果 cookie 浏览器关闭时过期，浏览器会在 cookie 重新启动后将其清除。</span><span class="sxs-lookup"><span data-stu-id="e62c6-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e62c6-205">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e62c6-206">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="e62c6-206">Absolute cookie expiration</span></span>

<span data-ttu-id="e62c6-207">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e62c6-208">若要创建持久 cookie ， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="e62c6-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e62c6-209">否则， cookie 创建时使用基于会话的生存期，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="e62c6-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e62c6-210">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="e62c6-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e62c6-211">下面的代码片段将创建一个标识，并将 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="e62c6-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e62c6-212">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="e62c6-212">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="e62c6-213">ASP.NET Core Identity 是一个完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="e62c6-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e62c6-214">但是， cookie 不能使用基于的身份验证提供程序 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e62c6-215">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="e62c6-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e62c6-216">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e62c6-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e62c6-217">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="e62c6-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e62c6-218">使用 **电子邮件** 地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="e62c6-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e62c6-219">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs* 文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e62c6-220">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e62c6-221">配置</span><span class="sxs-lookup"><span data-stu-id="e62c6-221">Configuration</span></span>

<span data-ttu-id="e62c6-222">如果应用程序不使用 [AspNetCore 元包](xref:fundamentals/metapackage-app)，请在项目文件中为 AspNetCore 创建包引用 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 包。</span><span class="sxs-lookup"><span data-stu-id="e62c6-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e62c6-223">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e62c6-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e62c6-225">`AuthenticationScheme` 如果有多个 cookie 身份验证实例，并且你想要 [使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="e62c6-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e62c6-226">将设置 `AuthenticationScheme` 为[ Cookie AuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 " Cookie s"。</span><span class="sxs-lookup"><span data-stu-id="e62c6-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e62c6-227">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="e62c6-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e62c6-228">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e62c6-229">当 cookie 未提供身份验证方案时 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它将使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e62c6-230">cookie默认情况下，身份验证的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e62c6-231">cookie当站点访问者未同意数据收集时，允许使用身份验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e62c6-232">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="e62c6-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e62c6-233">在 `Startup.Configure` 方法中，调用 `UseAuthentication` 方法以调用设置属性的身份验证中间件 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e62c6-234">`UseAuthentication`在调用或之前调用 `UseMvcWithDefaultRoute` 方法 `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e62c6-235"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="e62c6-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e62c6-236">在 `CookieAuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e62c6-237">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="e62c6-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="e62c6-238">[ Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)启用 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="e62c6-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e62c6-239">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="e62c6-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e62c6-240">使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供给 Cookie 策略中间件来控制 cookie cookie 在 cookie 追加或删除时处理和挂钩处理处理程序的全局特性。</span><span class="sxs-lookup"><span data-stu-id="e62c6-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e62c6-241">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="e62c6-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e62c6-242">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e62c6-243">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它会提升 cookie 其他不依赖于跨源请求处理的应用类型的安全级别。</span><span class="sxs-lookup"><span data-stu-id="e62c6-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e62c6-244">的 Cookie 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `Cookie.SameSite` ， `CookieAuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="e62c6-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e62c6-245">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e62c6-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e62c6-246">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="e62c6-246">Cookie.SameSite</span></span> | <span data-ttu-id="e62c6-247">结果 Cookie 。SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="e62c6-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e62c6-248">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-248">SameSiteMode.None</span></span>     | <span data-ttu-id="e62c6-249">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-249">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-250">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-251">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-252">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-252">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-253">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-254">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e62c6-255">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e62c6-256">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-256">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-257">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-258">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-259">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-260">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-261">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e62c6-262">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e62c6-263">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-263">SameSiteMode.None</span></span><br><span data-ttu-id="e62c6-264">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="e62c6-265">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="e62c6-266">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="e62c6-267">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="e62c6-268">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e62c6-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e62c6-269">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="e62c6-269">Create an authentication cookie</span></span>

<span data-ttu-id="e62c6-270">若要创建 cookie 保存用户信息，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e62c6-271">将序列化用户信息并将其存储在中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e62c6-272"><xref:System.Security.Claims.ClaimsIdentity>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="e62c6-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e62c6-273">`SignInAsync` 创建一个加密的 cookie ，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="e62c6-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e62c6-274">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e62c6-275">ASP.NET Core 的 [数据保护](xref:security/data-protection/using-data-protection) 系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="e62c6-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e62c6-276">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请 [将数据保护配置](xref:security/data-protection/configuration/overview) 为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="e62c6-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e62c6-277">注销</span><span class="sxs-lookup"><span data-stu-id="e62c6-277">Sign out</span></span>

<span data-ttu-id="e62c6-278">若要注销当前用户并将其删除 cookie ，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e62c6-279">如果 `CookieAuthenticationDefaults.AuthenticationScheme` (或 " Cookie s" ) 未用作方案 (例如 "Contoso Cookie " ) ，请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e62c6-280">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="e62c6-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e62c6-281">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="e62c6-281">React to back-end changes</span></span>

<span data-ttu-id="e62c6-282">一旦 cookie 创建， cookie 就是标识的单个源。</span><span class="sxs-lookup"><span data-stu-id="e62c6-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e62c6-283">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="e62c6-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e62c6-284">应用的 cookie 身份验证系统将继续根据身份验证处理请求 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e62c6-285">只要身份验证有效，用户就会保持登录到应用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e62c6-286"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 cookie 标识验证。</span><span class="sxs-lookup"><span data-stu-id="e62c6-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e62c6-287">验证 cookie 每个请求是否会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="e62c6-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e62c6-288">验证的一种方法 cookie 是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="e62c6-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e62c6-289">如果自用户颁发以来数据库尚未发生更改 cookie ，则无需重新对用户进行身份验证，前提 cookie 是用户仍有效。</span><span class="sxs-lookup"><span data-stu-id="e62c6-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e62c6-290">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="e62c6-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e62c6-291">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="e62c6-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e62c6-292">为了使 cookie 数据库根据值发生更改时失效 `LastChanged` ，请 cookie 使用 `LastChanged` 包含数据库中的当前值的声明创建 `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e62c6-293">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e62c6-294">下面是的示例实现 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e62c6-295">cookie在方法中注册服务期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e62c6-296">提供类的 [作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e62c6-297">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="e62c6-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e62c6-298">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e62c6-299">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="e62c6-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e62c6-300">验证 cookie 每个请求的所有用户的身份验证可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="e62c6-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e62c6-301">持久性 cookie</span><span class="sxs-lookup"><span data-stu-id="e62c6-301">Persistent cookies</span></span>

<span data-ttu-id="e62c6-302">你可能希望在 cookie 浏览器会话之间保持。</span><span class="sxs-lookup"><span data-stu-id="e62c6-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e62c6-303">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="e62c6-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e62c6-304">下面的代码片段将创建一个标识，并 cookie 在浏览器闭包中置对应。</span><span class="sxs-lookup"><span data-stu-id="e62c6-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e62c6-305">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="e62c6-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e62c6-306">如果 cookie 浏览器关闭时过期，浏览器会在 cookie 重新启动后将其清除。</span><span class="sxs-lookup"><span data-stu-id="e62c6-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e62c6-307">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="e62c6-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e62c6-308">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="e62c6-308">Absolute cookie expiration</span></span>

<span data-ttu-id="e62c6-309">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="e62c6-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e62c6-310">若要创建持久 cookie ， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="e62c6-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e62c6-311">否则， cookie 创建时使用基于会话的生存期，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="e62c6-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e62c6-312">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="e62c6-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e62c6-313">下面的代码片段将创建一个标识，并将 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="e62c6-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e62c6-314">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="e62c6-314">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e62c6-315">其他资源</span><span class="sxs-lookup"><span data-stu-id="e62c6-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e62c6-316">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="e62c6-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
