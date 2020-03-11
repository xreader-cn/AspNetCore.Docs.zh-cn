---
title: 使用 cookie 而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 了解如何使用 cookie 身份验证，而无需 ASP.NET Core 标识。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 64f881441a7a7f9a5529cb6ee5ce81142ccd69e6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653418"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="4dba2-103">使用 cookie 而无需 ASP.NET Core 标识的身份验证</span><span class="sxs-lookup"><span data-stu-id="4dba2-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="4dba2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4dba2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4dba2-105">ASP.NET Core 标识是完整、功能齐全的身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="4dba2-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4dba2-106">但是，可以使用不带 ASP.NET Core 标识的基于 cookie 的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="4dba2-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4dba2-107">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4dba2-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4dba2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4dba2-109">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4dba2-110">使用**电子邮件**地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="4dba2-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4dba2-111">用户在*页面/帐户/登录名. .cs*文件的 `AuthenticateUser` 方法中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4dba2-112">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4dba2-113">配置</span><span class="sxs-lookup"><span data-stu-id="4dba2-113">Configuration</span></span>

<span data-ttu-id="4dba2-114">如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="4dba2-115">在 `Startup.ConfigureServices` 方法中，创建具有 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 和 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 方法的身份验证中间件服务：</span><span class="sxs-lookup"><span data-stu-id="4dba2-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4dba2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递到 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4dba2-117">如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，`AuthenticationScheme` 会很有用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4dba2-118">将 `AuthenticationScheme` 设置为[CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 "cookie"。</span><span class="sxs-lookup"><span data-stu-id="4dba2-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4dba2-119">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="4dba2-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4dba2-120">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4dba2-121">如果未向 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>提供 cookie 身份验证方案，则使用 `CookieAuthenticationDefaults.AuthenticationScheme` （"Cookie"）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4dba2-122">默认情况下，身份验证 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4dba2-123">当站点访问者未同意数据收集时，允许使用身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4dba2-124">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4dba2-125">在 `Startup.Configure`中，调用 `UseAuthentication` 和 `UseAuthorization` 设置 `HttpContext.User` 属性，并为请求运行授权中间件。</span><span class="sxs-lookup"><span data-stu-id="4dba2-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="4dba2-126">调用 `UseEndpoints`之前调用 `UseAuthentication` 和 `UseAuthorization` 方法：</span><span class="sxs-lookup"><span data-stu-id="4dba2-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4dba2-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="4dba2-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4dba2-128">在服务配置中设置 `CookieAuthenticationOptions`，以便在 `Startup.ConfigureServices` 方法中进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="4dba2-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="4dba2-129">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="4dba2-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="4dba2-130">[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="4dba2-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4dba2-131">将中间件添加到应用处理管道是区分顺序的&mdash;它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="4dba2-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4dba2-132">使用提供给 Cookie 策略中间件的 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 来控制 cookie 处理的全局特性并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。</span><span class="sxs-lookup"><span data-stu-id="4dba2-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4dba2-133">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 以允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="4dba2-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4dba2-134">若要严格实施 `SameSiteMode.Strict`的同一站点策略，请设置 `MinimumSameSitePolicy`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4dba2-135">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="4dba2-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4dba2-136">`MinimumSameSitePolicy` 的 Cookie 策略中间件设置会根据下面的矩阵影响 `CookieAuthenticationOptions` 设置中 `Cookie.SameSite` 的设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4dba2-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4dba2-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4dba2-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="4dba2-138">Cookie.SameSite</span></span> | <span data-ttu-id="4dba2-139">生成的 SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="4dba2-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4dba2-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-140">SameSiteMode.None</span></span>     | <span data-ttu-id="4dba2-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-141">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-144">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4dba2-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4dba2-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-148">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4dba2-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4dba2-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-155">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="4dba2-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="4dba2-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="4dba2-161">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="4dba2-161">Create an authentication cookie</span></span>

<span data-ttu-id="4dba2-162">若要创建保存用户信息的 cookie，请构造一个 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4dba2-163">将对用户信息进行序列化并将其存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4dba2-164">使用任何所需的 <xref:System.Security.Claims.Claim>创建 <xref:System.Security.Claims.ClaimsIdentity>，并调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登录用户：</span><span class="sxs-lookup"><span data-stu-id="4dba2-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4dba2-165">`SignInAsync` 创建加密的 cookie，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4dba2-166">如果未指定 `AuthenticationScheme`，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4dba2-167">ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="4dba2-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4dba2-168">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="4dba2-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4dba2-169">注销</span><span class="sxs-lookup"><span data-stu-id="4dba2-169">Sign out</span></span>

<span data-ttu-id="4dba2-170">若要注销当前用户并删除其 cookie，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>：</span><span class="sxs-lookup"><span data-stu-id="4dba2-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4dba2-171">如果 `CookieAuthenticationDefaults.AuthenticationScheme` （或 "Cookie"）不用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4dba2-172">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4dba2-173">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="4dba2-173">React to back-end changes</span></span>

<span data-ttu-id="4dba2-174">创建 cookie 后，cookie 就是单一标识源。</span><span class="sxs-lookup"><span data-stu-id="4dba2-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4dba2-175">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="4dba2-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4dba2-176">应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。</span><span class="sxs-lookup"><span data-stu-id="4dba2-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4dba2-177">只要身份验证 cookie 有效，用户就会保持登录到应用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4dba2-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 事件可用于截获和重写 cookie 标识的验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4dba2-179">验证每个请求的 cookie 会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="4dba2-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4dba2-180">Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="4dba2-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4dba2-181">如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="4dba2-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4dba2-182">在示例应用中，数据库在 `IUserRepository` 中实现并存储 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="4dba2-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4dba2-183">当在数据库中更新用户时，`LastChanged` 值设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="4dba2-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4dba2-184">为了使数据库根据 `LastChanged` 值更改时使 cookie 无效，请使用包含数据库中当前 `LastChanged` 值的 `LastChanged` 声明创建 cookie：</span><span class="sxs-lookup"><span data-stu-id="4dba2-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="4dba2-185">若要为 `ValidatePrincipal` 事件实现替代，请在从 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生的类中编写具有以下签名的方法：</span><span class="sxs-lookup"><span data-stu-id="4dba2-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4dba2-186">下面是 `CookieAuthenticationEvents`的示例实现：</span><span class="sxs-lookup"><span data-stu-id="4dba2-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="4dba2-187">在 `Startup.ConfigureServices` 方法中，在 cookie 服务注册期间注册事件实例。</span><span class="sxs-lookup"><span data-stu-id="4dba2-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4dba2-188">提供 `CustomCookieAuthenticationEvents` 类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes)：</span><span class="sxs-lookup"><span data-stu-id="4dba2-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4dba2-189">请考虑这样一种情况：用户的名称更新&mdash;不会以任何方式影响安全性的决策。</span><span class="sxs-lookup"><span data-stu-id="4dba2-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4dba2-190">如果要以非破坏性的更新用户主体，请调用 `context.ReplacePrincipal`，并将 `context.ShouldRenew` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4dba2-191">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="4dba2-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4dba2-192">验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="4dba2-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="4dba2-193">永久性 cookie</span><span class="sxs-lookup"><span data-stu-id="4dba2-193">Persistent cookies</span></span>

<span data-ttu-id="4dba2-194">你可能希望 cookie 在浏览器会话之间保持不变。</span><span class="sxs-lookup"><span data-stu-id="4dba2-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4dba2-195">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="4dba2-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4dba2-196">下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4dba2-197">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4dba2-198">如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4dba2-199">将 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 设置为 `true` 在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>中：</span><span class="sxs-lookup"><span data-stu-id="4dba2-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="4dba2-200">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="4dba2-200">Absolute cookie expiration</span></span>

<span data-ttu-id="4dba2-201">绝对过期时间可使用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4dba2-202">若要创建持久性 cookie，还必须设置 `IsPersistent`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4dba2-203">否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="4dba2-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4dba2-204">设置 `ExpiresUtc` 后，它将覆盖 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>的 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值（如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4dba2-205">下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="4dba2-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4dba2-206">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="4dba2-207">ASP.NET Core 标识是完整、功能齐全的身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="4dba2-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4dba2-208">但是，可以使用不带 ASP.NET Core 标识的基于 cookie 的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="4dba2-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4dba2-209">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4dba2-210">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4dba2-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4dba2-211">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4dba2-212">使用**电子邮件**地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="4dba2-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4dba2-213">用户在*页面/帐户/登录名. .cs*文件的 `AuthenticateUser` 方法中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4dba2-214">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4dba2-215">配置</span><span class="sxs-lookup"><span data-stu-id="4dba2-215">Configuration</span></span>

<span data-ttu-id="4dba2-216">如果应用程序不使用[AspNetCore 元包](xref:fundamentals/metapackage-app)，请在 AspNetCore 包的项目文件中创建包引用（& [c](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) ）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="4dba2-217">在 `Startup.ConfigureServices` 方法中，创建具有 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 和 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 方法的身份验证中间件服务：</span><span class="sxs-lookup"><span data-stu-id="4dba2-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4dba2-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 传递到 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4dba2-219">如果有多个 cookie 身份验证实例，并且你想要[使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，`AuthenticationScheme` 会很有用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4dba2-220">将 `AuthenticationScheme` 设置为[CookieAuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)为方案提供值 "cookie"。</span><span class="sxs-lookup"><span data-stu-id="4dba2-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4dba2-221">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="4dba2-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4dba2-222">应用的身份验证方案不同于应用的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4dba2-223">如果未向 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>提供 cookie 身份验证方案，则使用 `CookieAuthenticationDefaults.AuthenticationScheme` （"Cookie"）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4dba2-224">默认情况下，身份验证 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4dba2-225">当站点访问者未同意数据收集时，允许使用身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4dba2-226">有关详细信息，请参阅 <xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4dba2-227">在 `Startup.Configure` 方法中，调用 `UseAuthentication` 方法来调用设置 `HttpContext.User` 属性的身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="4dba2-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4dba2-228">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc`之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="4dba2-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4dba2-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="4dba2-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4dba2-230">在服务配置中设置 `CookieAuthenticationOptions`，以便在 `Startup.ConfigureServices` 方法中进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="4dba2-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="4dba2-231">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="4dba2-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="4dba2-232">[Cookie 策略中间件](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)支持 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="4dba2-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4dba2-233">将中间件添加到应用处理管道是区分顺序的&mdash;它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="4dba2-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4dba2-234">使用提供给 Cookie 策略中间件的 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 来控制 cookie 处理的全局特性并在附加或删除 cookie 时挂钩到 cookie 处理处理程序。</span><span class="sxs-lookup"><span data-stu-id="4dba2-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4dba2-235">默认 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 以允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="4dba2-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4dba2-236">若要严格实施 `SameSiteMode.Strict`的同一站点策略，请设置 `MinimumSameSitePolicy`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4dba2-237">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它为不依赖于跨源请求处理的其他类型的应用提升了 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="4dba2-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4dba2-238">`MinimumSameSitePolicy` 的 Cookie 策略中间件设置会根据下面的矩阵影响 `CookieAuthenticationOptions` 设置中 `Cookie.SameSite` 的设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4dba2-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4dba2-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4dba2-240">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="4dba2-240">Cookie.SameSite</span></span> | <span data-ttu-id="4dba2-241">生成的 SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="4dba2-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4dba2-242">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-242">SameSiteMode.None</span></span>     | <span data-ttu-id="4dba2-243">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-243">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-244">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-245">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-246">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4dba2-249">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4dba2-250">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-250">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-251">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-252">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4dba2-256">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4dba2-257">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="4dba2-257">SameSiteMode.None</span></span><br><span data-ttu-id="4dba2-258">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="4dba2-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="4dba2-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="4dba2-260">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="4dba2-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="4dba2-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="4dba2-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="4dba2-263">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="4dba2-263">Create an authentication cookie</span></span>

<span data-ttu-id="4dba2-264">若要创建保存用户信息的 cookie，请构造一个 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="4dba2-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4dba2-265">将对用户信息进行序列化并将其存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4dba2-266">使用任何所需的 <xref:System.Security.Claims.Claim>创建 <xref:System.Security.Claims.ClaimsIdentity>，并调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登录用户：</span><span class="sxs-lookup"><span data-stu-id="4dba2-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4dba2-267">`SignInAsync` 创建加密的 cookie，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="4dba2-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4dba2-268">如果未指定 `AuthenticationScheme`，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4dba2-269">ASP.NET Core 的[数据保护](xref:security/data-protection/using-data-protection)系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="4dba2-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4dba2-270">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请[将数据保护配置](xref:security/data-protection/configuration/overview)为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="4dba2-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4dba2-271">注销</span><span class="sxs-lookup"><span data-stu-id="4dba2-271">Sign out</span></span>

<span data-ttu-id="4dba2-272">若要注销当前用户并删除其 cookie，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>：</span><span class="sxs-lookup"><span data-stu-id="4dba2-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4dba2-273">如果 `CookieAuthenticationDefaults.AuthenticationScheme` （或 "Cookie"）不用作方案（例如 "ContosoCookie"），请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4dba2-274">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4dba2-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4dba2-275">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="4dba2-275">React to back-end changes</span></span>

<span data-ttu-id="4dba2-276">创建 cookie 后，cookie 就是单一标识源。</span><span class="sxs-lookup"><span data-stu-id="4dba2-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4dba2-277">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="4dba2-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4dba2-278">应用的 cookie 身份验证系统将继续根据身份验证 cookie 处理请求。</span><span class="sxs-lookup"><span data-stu-id="4dba2-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4dba2-279">只要身份验证 cookie 有效，用户就会保持登录到应用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4dba2-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 事件可用于截获和重写 cookie 标识的验证。</span><span class="sxs-lookup"><span data-stu-id="4dba2-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4dba2-281">验证每个请求的 cookie 会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="4dba2-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4dba2-282">Cookie 验证的一种方法是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="4dba2-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4dba2-283">如果在颁发用户 cookie 后数据库尚未更改，则无需重新对用户进行身份验证，前提是其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="4dba2-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4dba2-284">在示例应用中，数据库在 `IUserRepository` 中实现并存储 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="4dba2-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4dba2-285">当在数据库中更新用户时，`LastChanged` 值设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="4dba2-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4dba2-286">为了使数据库根据 `LastChanged` 值更改时使 cookie 无效，请使用包含数据库中当前 `LastChanged` 值的 `LastChanged` 声明创建 cookie：</span><span class="sxs-lookup"><span data-stu-id="4dba2-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="4dba2-287">若要为 `ValidatePrincipal` 事件实现替代，请在从 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生的类中编写具有以下签名的方法：</span><span class="sxs-lookup"><span data-stu-id="4dba2-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4dba2-288">下面是 `CookieAuthenticationEvents`的示例实现：</span><span class="sxs-lookup"><span data-stu-id="4dba2-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="4dba2-289">在 `Startup.ConfigureServices` 方法中，在 cookie 服务注册期间注册事件实例。</span><span class="sxs-lookup"><span data-stu-id="4dba2-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4dba2-290">提供 `CustomCookieAuthenticationEvents` 类的[作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes)：</span><span class="sxs-lookup"><span data-stu-id="4dba2-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4dba2-291">请考虑这样一种情况：用户的名称更新&mdash;不会以任何方式影响安全性的决策。</span><span class="sxs-lookup"><span data-stu-id="4dba2-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4dba2-292">如果要以非破坏性的更新用户主体，请调用 `context.ReplacePrincipal`，并将 `context.ShouldRenew` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4dba2-293">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="4dba2-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4dba2-294">验证每个请求的所有用户的身份验证 cookie 可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="4dba2-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="4dba2-295">永久性 cookie</span><span class="sxs-lookup"><span data-stu-id="4dba2-295">Persistent cookies</span></span>

<span data-ttu-id="4dba2-296">你可能希望 cookie 在浏览器会话之间保持不变。</span><span class="sxs-lookup"><span data-stu-id="4dba2-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4dba2-297">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="4dba2-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4dba2-298">下面的代码片段创建一个标识，并在浏览器闭包置了相应的 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4dba2-299">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="4dba2-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4dba2-300">如果关闭浏览器时 cookie 过期，则在重新启动后，浏览器将清除 cookie。</span><span class="sxs-lookup"><span data-stu-id="4dba2-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4dba2-301">将 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 设置为 `true` 在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>中：</span><span class="sxs-lookup"><span data-stu-id="4dba2-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="4dba2-302">绝对 cookie 过期</span><span class="sxs-lookup"><span data-stu-id="4dba2-302">Absolute cookie expiration</span></span>

<span data-ttu-id="4dba2-303">绝对过期时间可使用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4dba2-304">若要创建持久性 cookie，还必须设置 `IsPersistent`。</span><span class="sxs-lookup"><span data-stu-id="4dba2-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4dba2-305">否则，cookie 是使用基于会话的生存期创建的，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="4dba2-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4dba2-306">设置 `ExpiresUtc` 后，它将覆盖 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>的 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 选项的值（如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="4dba2-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4dba2-307">下面的代码片段将创建一个标识和对应的 cookie，此 cookie 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="4dba2-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4dba2-308">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="4dba2-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4dba2-309">其他资源</span><span class="sxs-lookup"><span data-stu-id="4dba2-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="4dba2-310">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="4dba2-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
