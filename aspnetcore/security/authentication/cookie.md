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
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="59724-103">使用 cookie 而无需 ASP.NET Core 标识的身份验证</span><span class="sxs-lookup"><span data-stu-id="59724-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="59724-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="59724-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="59724-105">ASP.NET Core 标识用于创建和维护的登录名是完整的全功能的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="59724-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="59724-106">但是，可以使用基于 cookie 的身份验证身份验证提供程序，没有 ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="59724-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="59724-107">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="59724-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="59724-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="59724-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59724-109">出于演示目的，示例应用程序中，假设用户、 Maria Rodriguez 的用户帐户是硬编码到应用程序。</span><span class="sxs-lookup"><span data-stu-id="59724-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="59724-110">使用**电子邮件**用户名`maria.rodriguez@contoso.com`和任何密码以登录用户。</span><span class="sxs-lookup"><span data-stu-id="59724-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="59724-111">用户进行身份验证中`AuthenticateUser`中的方法*Pages/Account/Login.cshtml.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="59724-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="59724-112">在实际示例中，用户会根据数据库身份验证。</span><span class="sxs-lookup"><span data-stu-id="59724-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="59724-113">配置</span><span class="sxs-lookup"><span data-stu-id="59724-113">Configuration</span></span>

<span data-ttu-id="59724-114">如果应用不使用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，在项目文件中创建的包引用[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)包。</span><span class="sxs-lookup"><span data-stu-id="59724-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="59724-115">在中`Startup.ConfigureServices`方法中，创建具有的身份验证中间件服务<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>和<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>方法：</span><span class="sxs-lookup"><span data-stu-id="59724-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="59724-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递给`AddAuthentication`设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="59724-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="59724-117">`AuthenticationScheme` 有多个实例的 cookie 身份验证并要时很有用[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。</span><span class="sxs-lookup"><span data-stu-id="59724-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="59724-118">设置`AuthenticationScheme`到[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)方案提供的值为"Cookie"。</span><span class="sxs-lookup"><span data-stu-id="59724-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="59724-119">你可以提供任何字符串值，用于区分方案。</span><span class="sxs-lookup"><span data-stu-id="59724-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="59724-120">应用程序的身份验证方案是不同的应用程序的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="59724-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="59724-121">当 cookie 身份验证方案不提供给<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>，它使用`CookieAuthenticationDefaults.AuthenticationScheme`("Cookie")。</span><span class="sxs-lookup"><span data-stu-id="59724-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="59724-122">身份验证 cookie<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>属性设置为`true`默认情况下。</span><span class="sxs-lookup"><span data-stu-id="59724-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="59724-123">网站访问者尚未同意数据收集时允许使用身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="59724-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="59724-124">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="59724-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="59724-125">在中`Startup.Configure`方法中，调用`UseAuthentication`方法调用设置身份验证中间件`HttpContext.User`属性。</span><span class="sxs-lookup"><span data-stu-id="59724-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="59724-126">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="59724-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="59724-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="59724-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="59724-128">设置`CookieAuthenticationOptions`中的身份验证的服务配置中`Startup.ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="59724-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="59724-129">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="59724-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="59724-130">[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)使 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="59724-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="59724-131">到应用程序处理管道添加中间件是敏感的顺序&mdash;它只影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="59724-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="59724-132">使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>提供给 Cookie 策略中间件时追加或删除 cookie 的 cookie 处理和挂钩全局特性控制到 cookie 处理程序。</span><span class="sxs-lookup"><span data-stu-id="59724-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="59724-133">默认值<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>值是`SameSiteMode.Lax`允许 OAuth2 身份验证。</span><span class="sxs-lookup"><span data-stu-id="59724-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="59724-134">若要严格强制实施的同一站点策略`SameSiteMode.Strict`，请将`MinimumSameSitePolicy`。</span><span class="sxs-lookup"><span data-stu-id="59724-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="59724-135">尽管此设置会中断的 OAuth2 和其他跨域身份验证方案，它会提升为其他类型的应用不依赖于跨域请求处理的 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="59724-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="59724-136">Cookie 策略中间件设置`MinimumSameSitePolicy`可能会影响的设置`Cookie.SameSite`中`CookieAuthenticationOptions`根据下面的表格的设置。</span><span class="sxs-lookup"><span data-stu-id="59724-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="59724-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="59724-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="59724-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="59724-138">Cookie.SameSite</span></span> | <span data-ttu-id="59724-139">最终的 Cookie.SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="59724-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="59724-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="59724-140">SameSiteMode.None</span></span>     | <span data-ttu-id="59724-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="59724-141">SameSiteMode.None</span></span><br><span data-ttu-id="59724-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="59724-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="59724-144">SameSiteMode.None</span></span><br><span data-ttu-id="59724-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="59724-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="59724-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="59724-148">SameSiteMode.None</span></span><br><span data-ttu-id="59724-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="59724-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="59724-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="59724-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="59724-155">SameSiteMode.None</span></span><br><span data-ttu-id="59724-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="59724-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="59724-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="59724-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="59724-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="59724-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="59724-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="59724-161">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="59724-161">Create an authentication cookie</span></span>

<span data-ttu-id="59724-162">若要创建 cookie 保存用户的信息，请构造<xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="59724-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="59724-163">用户信息序列化并存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="59724-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="59724-164">创建<xref:System.Security.Claims.ClaimsIdentity>与任何所需<xref:System.Security.Claims.Claim>s 和调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>以登录用户：</span><span class="sxs-lookup"><span data-stu-id="59724-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="59724-165">`SignInAsync` 创建一个加密的 cookie，并将其添加到当前响应。</span><span class="sxs-lookup"><span data-stu-id="59724-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="59724-166">如果`AuthenticationScheme`未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="59724-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="59724-167">ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="59724-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="59724-168">对于多台计算机上托管的应用，负载平衡应用程序，或使用 web 场[配置数据保护](xref:security/data-protection/configuration/overview)使用相同密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="59724-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="59724-169">注销</span><span class="sxs-lookup"><span data-stu-id="59724-169">Sign out</span></span>

<span data-ttu-id="59724-170">若要注销当前用户，然后删除其 cookie，调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="59724-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="59724-171">如果`CookieAuthenticationDefaults.AuthenticationScheme`（或"Cookie"） 未使用的方案 (例如，"ContosoCookie")，提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="59724-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="59724-172">否则，使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="59724-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="59724-173">对后端更改做出响应</span><span class="sxs-lookup"><span data-stu-id="59724-173">React to back-end changes</span></span>

<span data-ttu-id="59724-174">一旦创建了 cookie，cookie 是标识的单个来源。</span><span class="sxs-lookup"><span data-stu-id="59724-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="59724-175">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="59724-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="59724-176">应用程序的 cookie 身份验证系统将继续处理基于身份验证 cookie 的请求。</span><span class="sxs-lookup"><span data-stu-id="59724-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="59724-177">只要是有效的身份验证 cookie，用户就会保持登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="59724-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="59724-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写的 cookie 身份验证。</span><span class="sxs-lookup"><span data-stu-id="59724-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="59724-179">验证对每个请求的 cookie 应吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="59724-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="59724-180">Cookie 验证的一种方法取决于跟踪的用户数据库发生更改时。</span><span class="sxs-lookup"><span data-stu-id="59724-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="59724-181">如果数据库未已发生更改，因为发布用户的 cookie，则无需重新验证用户，如果其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="59724-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="59724-182">在示例应用中，在数据库中实现`IUserRepository`，并将存储`LastChanged`值。</span><span class="sxs-lookup"><span data-stu-id="59724-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="59724-183">在数据库中，更新用户时`LastChanged`值设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="59724-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="59724-184">为了使 cookie 无效时的数据库更改基于`LastChanged`值时，请创建与 cookie`LastChanged`包含当前声明`LastChanged`值位于数据库中：</span><span class="sxs-lookup"><span data-stu-id="59724-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="59724-185">若要实现的替代`ValidatePrincipal`事件，编写一个方法具有以下签名在派生类中<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="59724-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="59724-186">以下是示例实现`CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="59724-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="59724-187">在中的 cookie 服务注册期间注册的事件实例`Startup.ConfigureServices`方法。</span><span class="sxs-lookup"><span data-stu-id="59724-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="59724-188">提供[范围内服务注册](xref:fundamentals/dependency-injection#service-lifetimes)为你`CustomCookieAuthenticationEvents`类：</span><span class="sxs-lookup"><span data-stu-id="59724-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="59724-189">请考虑在其中更新用户的名称的情况下&mdash;不会影响任何方式的安全决策。</span><span class="sxs-lookup"><span data-stu-id="59724-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="59724-190">如果您需要非破坏性地更新用户主体，请调用`context.ReplacePrincipal`并设置`context.ShouldRenew`属性设置为`true`。</span><span class="sxs-lookup"><span data-stu-id="59724-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="59724-191">此处所述的方法上的每个请求触发。</span><span class="sxs-lookup"><span data-stu-id="59724-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="59724-192">验证每个请求上的所有用户的身份验证 cookie 可能会导致应用程序对较大性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="59724-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="59724-193">永久 cookie</span><span class="sxs-lookup"><span data-stu-id="59724-193">Persistent cookies</span></span>

<span data-ttu-id="59724-194">你可能想要在浏览器会话之间持久保存的 cookie。</span><span class="sxs-lookup"><span data-stu-id="59724-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="59724-195">此持久性仅应启用"记住我"复选框在登录或类似机制显式用户同意。</span><span class="sxs-lookup"><span data-stu-id="59724-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="59724-196">下面的代码段创建的标识和相应可以幸存，但通过浏览器闭包的 cookie。</span><span class="sxs-lookup"><span data-stu-id="59724-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="59724-197">遵循以前配置任何滑动过期设置。</span><span class="sxs-lookup"><span data-stu-id="59724-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="59724-198">如果 cookie 已过期浏览器处于关闭状态时，浏览器中清除 cookie 后重新启动它。</span><span class="sxs-lookup"><span data-stu-id="59724-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="59724-199">设置<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>到`true`中<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="59724-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="59724-200">绝对 cookie 到期时间</span><span class="sxs-lookup"><span data-stu-id="59724-200">Absolute cookie expiration</span></span>

<span data-ttu-id="59724-201">可使用设置绝对过期时间<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>。</span><span class="sxs-lookup"><span data-stu-id="59724-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="59724-202">若要创建持久 cookie`IsPersistent`还必须设置。</span><span class="sxs-lookup"><span data-stu-id="59724-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="59724-203">否则为 cookie 使用基于会话的生存期内创建的并且可能过期之前或之后它所持有的身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="59724-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="59724-204">当`ExpiresUtc`，则它将重写的值<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>的选项<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>，如果设置。</span><span class="sxs-lookup"><span data-stu-id="59724-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="59724-205">下面的代码段创建的标识和相应的 cookie 的持续时间为 20 分钟。</span><span class="sxs-lookup"><span data-stu-id="59724-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="59724-206">这会忽略以前配置的任何滑动过期设置。</span><span class="sxs-lookup"><span data-stu-id="59724-206">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="59724-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="59724-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="59724-208">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="59724-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
