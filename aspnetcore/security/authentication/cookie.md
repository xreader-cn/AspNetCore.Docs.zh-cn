---
title: 使用 cookie 而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 使用 cookie 而无需 ASP.NET Core 标识的身份验证的说明
ms.author: riande
ms.date: 10/11/2017
uid: security/authentication/cookie
ms.openlocfilehash: f05e5b83359ec1739115293e092eaed0c811c046
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854375"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="ac7dd-103">使用 cookie 而无需 ASP.NET Core 标识的身份验证</span><span class="sxs-lookup"><span data-stu-id="ac7dd-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="ac7dd-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ac7dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ac7dd-105">如你所见在早期的身份验证主题中， [ASP.NET Core 标识](xref:security/authentication/identity)完成、 功能完备的身份验证提供程序是用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="ac7dd-106">但是，你可能想要使用基于 cookie 的身份验证有时使用您自己的自定义身份验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="ac7dd-107">你可以使用基于 cookie 的身份验证作为独立身份验证提供程序，没有 ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="ac7dd-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ac7dd-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ac7dd-109">出于演示目的，示例应用程序中，假设用户、 Maria Rodriguez 的用户帐户是硬编码到应用程序。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="ac7dd-110">使用电子邮件用户名"maria.rodriguez@contoso.com"和任何密码以登录用户。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="ac7dd-111">用户进行身份验证中`AuthenticateUser`中的方法*Pages/Account/Login.cshtml.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="ac7dd-112">在实际示例中，用户会根据数据库身份验证。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="ac7dd-113">有关从 ASP.NET Core 迁移基于 cookie 的身份验证信息 1.x 到 2.0，请参阅[迁移身份验证和标识到 ASP.NET Core 2.0 主题 （基于 Cookie 的身份验证）](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="ac7dd-114">若要使用 ASP.NET Core 标识，请参阅[标识简介](xref:security/authentication/identity)主题。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="ac7dd-115">配置</span><span class="sxs-lookup"><span data-stu-id="ac7dd-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ac7dd-116">如果应用不使用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，在项目文件中创建的包引用[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)包 (版本 2.1.0 或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="ac7dd-117">在中`ConfigureServices`方法中，创建具有的身份验证中间件服务`AddAuthentication`和`AddCookie`方法：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="ac7dd-118">`AuthenticationScheme` 传递给`AddAuthentication`设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="ac7dd-119">`AuthenticationScheme` 有多个实例的 cookie 身份验证并要时很有用[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="ac7dd-120">设置`AuthenticationScheme`到`CookieAuthenticationDefaults.AuthenticationScheme`方案提供的值为"Cookie"。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="ac7dd-121">你可以提供任何字符串值，用于区分方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="ac7dd-122">应用程序的身份验证方案是不同的应用程序的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="ac7dd-123">当 cookie 身份验证方案不提供给<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>，它使用[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookie")。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="ac7dd-124">在 `Configure` 方法中，使用 `UseAuthentication` 方法调用用于设置 `HttpContext.User` 属性的身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-124">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="ac7dd-125">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-125">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="ac7dd-126">**AddCookie Options**</span><span class="sxs-lookup"><span data-stu-id="ac7dd-126">**AddCookie Options**</span></span>

<span data-ttu-id="ac7dd-127">[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0)类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-127">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="ac7dd-128">选项</span><span class="sxs-lookup"><span data-stu-id="ac7dd-128">Option</span></span> | <span data-ttu-id="ac7dd-129">描述</span><span class="sxs-lookup"><span data-stu-id="ac7dd-129">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ac7dd-130">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="ac7dd-130">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-131">提供的路径以提供 302 已找到 （URL 重定向） 时触发的`HttpContext.ForbidAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-131">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="ac7dd-132">默认值为 `/Account/AccessDenied`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-132">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="ac7dd-133">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="ac7dd-133">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-134">要用于的颁发者[颁发者](/dotnet/api/system.security.claims.claim.issuer)上创建由 cookie 身份验证服务的任何声明的属性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-134">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="ac7dd-135">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="ac7dd-135">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-136">Cookie 提供服务位置的域名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-136">The domain name where the cookie is served.</span></span> <span data-ttu-id="ac7dd-137">默认情况下，这是请求的主机名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-137">By default, this is the host name of the request.</span></span> <span data-ttu-id="ac7dd-138">在浏览器仅将 cookie 在请求中发送到匹配的主机名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-138">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="ac7dd-139">您可能希望调整此项是在你的域中有 cookie 提供给任何主机。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-139">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="ac7dd-140">例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`，和`staging.www.contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-140">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="ac7dd-141">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="ac7dd-141">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-142">指示是否 cookie 应只允许到服务器可访问的标志。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-142">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="ac7dd-143">此值更改为`false`允许客户端脚本访问 cookie，并可能会打开 cookie 被盗应用应您的应用程序具有[跨站点脚本 (XSS)](xref:security/cross-site-scripting)漏洞。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-143">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="ac7dd-144">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-144">The default value is `true`.</span></span> |
| [<span data-ttu-id="ac7dd-145">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="ac7dd-145">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-146">设置的 cookie 的名称。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-146">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="ac7dd-147">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="ac7dd-147">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-148">用于隔离同一主机名上运行的应用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-148">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="ac7dd-149">如果必须在运行的应用`/app1`并且想要将 cookie 限制为该应用，设置`CookiePath`属性设置为`/app1`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-149">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="ac7dd-150">通过此操作，cookie 是仅可在请求上`/app1`和其下的任何应用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-150">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="ac7dd-151">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="ac7dd-151">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-152">指示浏览器是否应允许要附加到同一站点的请求的 cookie (`SameSiteMode.Strict`) 或使用安全的 HTTP 方法和同一站点的请求的跨站点请求 (`SameSiteMode.Lax`)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-152">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="ac7dd-153">如果设置为`SameSiteMode.None`，未设置的 cookie 标头值。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-153">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="ac7dd-154">请注意， [Cookie 策略中间件](#cookie-policy-middleware)可能会覆盖你提供的值。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-154">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="ac7dd-155">若要支持 OAuth 身份验证，默认值是`SameSiteMode.Lax`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-155">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="ac7dd-156">有关详细信息，请参阅[OAuth 身份验证由于 SameSite cookie 策略中断](https://github.com/aspnet/Security/issues/1231)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-156">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="ac7dd-157">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="ac7dd-157">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-158">一个标志，指示是否创建的 cookie 应限制为 HTTPS (`CookieSecurePolicy.Always`)，HTTP 或 HTTPS (`CookieSecurePolicy.None`)，或与请求相同的协议 (`CookieSecurePolicy.SameAsRequest`)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-158">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="ac7dd-159">默认值为 `CookieSecurePolicy.SameAsRequest`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-159">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="ac7dd-160">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="ac7dd-160">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-161">集`DataProtectionProvider`用于创建默认`TicketDataFormat`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-161">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="ac7dd-162">如果`TicketDataFormat`属性设置，`DataProtectionProvider`选项不会继续使用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-162">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="ac7dd-163">如果未提供，则使用应用程序的默认数据保护提供程序。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-163">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="ac7dd-164">事件</span><span class="sxs-lookup"><span data-stu-id="ac7dd-164">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-165">该处理程序处理的特定点上提供的应用程序控制的提供程序上调用方法。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-165">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="ac7dd-166">如果`Events`不提供的默认实例提供时才调用这些方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-166">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="ac7dd-167">EventsType</span><span class="sxs-lookup"><span data-stu-id="ac7dd-167">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-168">用作服务类型，以获取`Events`实例而不是属性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-168">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="ac7dd-169">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="ac7dd-169">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-170">`TimeSpan`后将存储在 cookie 的身份验证票证到期。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-170">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="ac7dd-171">`ExpireTimeSpan` 添加到当前时间来创建票证的到期时间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-171">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="ac7dd-172">`ExpiredTimeSpan`值始终会进入加密 AuthTicket 验证服务器。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-172">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="ac7dd-173">此外可能进入[Set-cookie](https://tools.ietf.org/html/rfc6265#section-4.1)标头，但仅当`IsPersistent`设置。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-173">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="ac7dd-174">若要设置`IsPersistent`到`true`，配置[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties)传递给`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-174">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="ac7dd-175">默认值`ExpireTimeSpan`为 14 天。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-175">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="ac7dd-176">LoginPath</span><span class="sxs-lookup"><span data-stu-id="ac7dd-176">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-177">提供的路径以提供 302 已找到 （URL 重定向） 时触发的`HttpContext.ChallengeAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-177">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="ac7dd-178">生成 401 的当前 URL 添加到`LoginPath`作为查询字符串参数的名为`ReturnUrlParameter`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-178">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="ac7dd-179">一次请求`LoginPath`授予新登录标识，`ReturnUrlParameter`值用于将浏览器重定向回导致原始未授权的状态代码的 URL。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-179">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="ac7dd-180">默认值为 `/Account/Login`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-180">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="ac7dd-181">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="ac7dd-181">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-182">如果`LogoutPath`提供给处理程序，则将重定向到该路径的请求值的基础`ReturnUrlParameter`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-182">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="ac7dd-183">默认值为 `/Account/Logout`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-183">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="ac7dd-184">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="ac7dd-184">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-185">确定由 302 已找到 （URL 重定向） 响应的处理程序追加查询字符串参数的名称。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-185">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="ac7dd-186">`ReturnUrlParameter` 请求到达时，将使用`LoginPath`或`LogoutPath`来执行登录或注销操作后返回到原始 URL 的浏览器。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-186">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="ac7dd-187">默认值为 `ReturnUrl`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-187">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="ac7dd-188">SessionStore</span><span class="sxs-lookup"><span data-stu-id="ac7dd-188">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-189">一个可选容器，用于标识存储在请求之间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-189">An optional container used to store identity across requests.</span></span> <span data-ttu-id="ac7dd-190">使用时，只将会话标识符发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-190">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="ac7dd-191">`SessionStore` 可以用于缓解大型标识的潜在问题。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-191">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="ac7dd-192">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="ac7dd-192">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-193">指示是否应动态颁发包含更新的到期时间的新 cookie 的标志。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-193">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="ac7dd-194">这可以在任何请求，其中当前 cookie 过期时间已超过 50%过期。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-194">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="ac7dd-195">向前移动新的到期日期为当前日期加上`ExpireTimespan`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-195">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="ac7dd-196">[绝对 cookie 到期时间](xref:security/authentication/cookie#absolute-cookie-expiration)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-196">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="ac7dd-197">绝对过期时间可以通过限制身份验证 cookie 是有效的时间量来提高您的应用程序的安全性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-197">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="ac7dd-198">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-198">The default value is `true`.</span></span> |
| [<span data-ttu-id="ac7dd-199">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="ac7dd-199">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-200">`TicketDataFormat`用于保护和取消保护标识和存储在 cookie 值中的其他属性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-200">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="ac7dd-201">如果未提供，`TicketDataFormat`使用创建[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-201">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="ac7dd-202">验证</span><span class="sxs-lookup"><span data-stu-id="ac7dd-202">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="ac7dd-203">检查的选项是有效的方法。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-203">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="ac7dd-204">设置`CookieAuthenticationOptions`中的身份验证的服务配置中`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-204">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ac7dd-205">ASP.NET Core 1.x 使用 cookie[中间件](xref:fundamentals/middleware/index)，序列化为一个用户主体的加密 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-205">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="ac7dd-206">在后续请求中，对 cookie 进行验证，并重新创建和分配给主体`HttpContext.User`属性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-206">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="ac7dd-207">安装[Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)在项目中的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-207">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="ac7dd-208">此包包含 cookie 中间件。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-208">This package contains the cookie middleware.</span></span>

<span data-ttu-id="ac7dd-209">使用`UseCookieAuthentication`中的方法`Configure`中的方法您*Startup.cs*文件，然后`UseMvc`或`UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="ac7dd-209">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

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

<span data-ttu-id="ac7dd-210">**CookieAuthenticationOptions 选项**</span><span class="sxs-lookup"><span data-stu-id="ac7dd-210">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="ac7dd-211">[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1)类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-211">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="ac7dd-212">选项</span><span class="sxs-lookup"><span data-stu-id="ac7dd-212">Option</span></span> | <span data-ttu-id="ac7dd-213">描述</span><span class="sxs-lookup"><span data-stu-id="ac7dd-213">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ac7dd-214">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="ac7dd-214">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-215">设置身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-215">Sets the authentication scheme.</span></span> <span data-ttu-id="ac7dd-216">`AuthenticationScheme` 时，可以有多个实例的身份验证，并且你想要[与特定方案授权](xref:security/authorization/limitingidentitybyscheme)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-216">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="ac7dd-217">设置`AuthenticationScheme`到`CookieAuthenticationDefaults.AuthenticationScheme`方案提供的值为"Cookie"。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-217">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="ac7dd-218">你可以提供任何字符串值，用于区分方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-218">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="ac7dd-219">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="ac7dd-219">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-220">设置一个值，以指示 cookie 身份验证应在每个请求上运行并尝试验证并重新构造它创建的任何序列化的主体。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-220">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="ac7dd-221">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="ac7dd-221">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-222">如果为 true，则身份验证中间件处理自动挑战。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-222">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="ac7dd-223">如果为 false，身份验证中间件仅更改时进行显式指定响应`AuthenticationScheme`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-223">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="ac7dd-224">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="ac7dd-224">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-225">要用于的颁发者[颁发者](/dotnet/api/system.security.claims.claim.issuer)上创建的 cookie 身份验证中间件的任何声明的属性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-225">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="ac7dd-226">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="ac7dd-226">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-227">Cookie 提供服务位置的域名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-227">The domain name where the cookie is served.</span></span> <span data-ttu-id="ac7dd-228">默认情况下，这是请求的主机名。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-228">By default, this is the host name of the request.</span></span> <span data-ttu-id="ac7dd-229">在浏览器只起到匹配的主机名的 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-229">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="ac7dd-230">您可能希望调整此项是在你的域中有 cookie 提供给任何主机。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-230">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="ac7dd-231">例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`，和`staging.www.contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-231">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="ac7dd-232">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="ac7dd-232">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-233">指示是否 cookie 应只允许到服务器可访问的标志。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-233">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="ac7dd-234">此值更改为`false`允许客户端脚本访问 cookie，并可能会打开 cookie 被盗应用应您的应用程序具有[跨站点脚本 (XSS)](xref:security/cross-site-scripting)漏洞。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-234">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="ac7dd-235">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-235">The default value is `true`.</span></span> |
| [<span data-ttu-id="ac7dd-236">CookiePath</span><span class="sxs-lookup"><span data-stu-id="ac7dd-236">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-237">用于隔离同一主机名上运行的应用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-237">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="ac7dd-238">如果必须在运行的应用`/app1`并且想要将 cookie 限制为该应用，设置`CookiePath`属性设置为`/app1`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-238">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="ac7dd-239">通过此操作，cookie 是仅可在请求上`/app1`和其下的任何应用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-239">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="ac7dd-240">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="ac7dd-240">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-241">一个标志，指示是否创建的 cookie 应限制为 HTTPS (`CookieSecurePolicy.Always`)，HTTP 或 HTTPS (`CookieSecurePolicy.None`)，或与请求相同的协议 (`CookieSecurePolicy.SameAsRequest`)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-241">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="ac7dd-242">默认值为 `CookieSecurePolicy.SameAsRequest`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-242">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="ac7dd-243">说明</span><span class="sxs-lookup"><span data-stu-id="ac7dd-243">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-244">有关提供给应用程序的身份验证类型的其他信息。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-244">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="ac7dd-245">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="ac7dd-245">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-246">`TimeSpan`后将身份验证票证到期。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-246">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="ac7dd-247">它被添加到当前时间来创建票证的到期时间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-247">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="ac7dd-248">若要使用`ExpireTimeSpan`，则必须设置`IsPersistent`到`true`中`AuthenticationProperties`传递给`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-248">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="ac7dd-249">默认值为 14 天。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-249">The default value is 14 days.</span></span> |
| [<span data-ttu-id="ac7dd-250">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="ac7dd-250">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="ac7dd-251">一个标志，指示 cookie 的到期日期重置多个的下半部分`ExpireTimeSpan`经过一段时间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-251">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="ac7dd-252">新的 exipiration 时间向前移动，为当前日期加上`ExpireTimespan`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-252">The new exipiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="ac7dd-253">[绝对 cookie 到期时间](xref:security/authentication/cookie#absolute-cookie-expiration)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-253">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="ac7dd-254">绝对过期时间可以通过限制身份验证 cookie 是有效的时间量来提高您的应用程序的安全性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-254">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="ac7dd-255">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-255">The default value is `true`.</span></span> |

<span data-ttu-id="ac7dd-256">设置`CookieAuthenticationOptions`Cookie 身份验证中间件中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-256">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="ac7dd-257">Cookie 策略中间件</span><span class="sxs-lookup"><span data-stu-id="ac7dd-257">Cookie Policy Middleware</span></span>

<span data-ttu-id="ac7dd-258">[Cookie 策略中间件](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware)使应用程序中的 cookie 策略功能。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-258">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="ac7dd-259">到应用程序处理管道添加中间件是顺序敏感;它只影响在管道中注册后的组件。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-259">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="ac7dd-260">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)提供给 Cookie 策略中间件可用于控制时追加或删除 cookie 的 cookie 处理和挂钩全局特性 cookie 处理程序。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-260">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="ac7dd-261">属性</span><span class="sxs-lookup"><span data-stu-id="ac7dd-261">Property</span></span> | <span data-ttu-id="ac7dd-262">描述</span><span class="sxs-lookup"><span data-stu-id="ac7dd-262">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="ac7dd-263">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="ac7dd-263">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="ac7dd-264">会影响是否 cookie 必须 HttpOnly，该值一个标志，指示是否 cookie 应只允许到服务器可访问。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-264">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="ac7dd-265">默认值为 `HttpOnlyPolicy.None`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-265">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="ac7dd-266">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="ac7dd-266">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="ac7dd-267">影响的 cookie 的同一站点属性 （见下文）。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-267">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="ac7dd-268">默认值为 `SameSiteMode.Lax`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-268">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="ac7dd-269">此选项仅供 ASP.NET Core 2.0 +。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-269">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="ac7dd-270">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="ac7dd-270">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="ac7dd-271">当会追加一个 cookie 时调用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-271">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="ac7dd-272">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="ac7dd-272">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="ac7dd-273">当删除 cookie 时调用。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-273">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="ac7dd-274">Secure</span><span class="sxs-lookup"><span data-stu-id="ac7dd-274">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="ac7dd-275">会影响是否 cookie 必须是安全。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-275">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="ac7dd-276">默认值为 `CookieSecurePolicy.None`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-276">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="ac7dd-277">**MinimumSameSitePolicy** (ASP.NET Core 2.0 + 仅)</span><span class="sxs-lookup"><span data-stu-id="ac7dd-277">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="ac7dd-278">默认值`MinimumSameSitePolicy`值是`SameSiteMode.Lax`允许 OAuth2 身份验证。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-278">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="ac7dd-279">若要严格强制实施的同一站点策略`SameSiteMode.Strict`，请将`MinimumSameSitePolicy`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-279">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="ac7dd-280">尽管此设置会中断的 OAuth2 和其他跨域身份验证方案，它会提升为其他类型的应用不依赖于跨域请求处理的 cookie 安全级别。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-280">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="ac7dd-281">Cookie 策略中间件设置`MinimumSameSitePolicy`可能会影响您的设置的`Cookie.SameSite`中`CookieAuthenticationOptions`根据下面的表格的设置。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-281">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="ac7dd-282">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="ac7dd-282">MinimumSameSitePolicy</span></span> | <span data-ttu-id="ac7dd-283">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="ac7dd-283">Cookie.SameSite</span></span> | <span data-ttu-id="ac7dd-284">最终的 Cookie.SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="ac7dd-284">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="ac7dd-285">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="ac7dd-285">SameSiteMode.None</span></span>     | <span data-ttu-id="ac7dd-286">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="ac7dd-286">SameSiteMode.None</span></span><br><span data-ttu-id="ac7dd-287">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-287">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-288">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-288">SameSiteMode.Strict</span></span> | <span data-ttu-id="ac7dd-289">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="ac7dd-289">SameSiteMode.None</span></span><br><span data-ttu-id="ac7dd-290">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-290">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-291">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-291">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="ac7dd-292">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-292">SameSiteMode.Lax</span></span>      | <span data-ttu-id="ac7dd-293">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="ac7dd-293">SameSiteMode.None</span></span><br><span data-ttu-id="ac7dd-294">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-294">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-295">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-295">SameSiteMode.Strict</span></span> | <span data-ttu-id="ac7dd-296">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-296">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-297">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-298">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-298">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="ac7dd-299">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-299">SameSiteMode.Strict</span></span>   | <span data-ttu-id="ac7dd-300">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="ac7dd-300">SameSiteMode.None</span></span><br><span data-ttu-id="ac7dd-301">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="ac7dd-301">SameSiteMode.Lax</span></span><br><span data-ttu-id="ac7dd-302">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-302">SameSiteMode.Strict</span></span> | <span data-ttu-id="ac7dd-303">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-303">SameSiteMode.Strict</span></span><br><span data-ttu-id="ac7dd-304">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-304">SameSiteMode.Strict</span></span><br><span data-ttu-id="ac7dd-305">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="ac7dd-305">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="ac7dd-306">创建身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="ac7dd-306">Create an authentication cookie</span></span>

<span data-ttu-id="ac7dd-307">若要创建保存用户信息的 cookie，必须构造[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-307">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="ac7dd-308">用户信息序列化并存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-308">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ac7dd-309">创建[ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity)包含任何必需[声明](/dotnet/api/system.security.claims.claim)s 和调用[SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0)以登录用户：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-309">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ac7dd-310">调用[SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1)以登录用户：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-310">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="ac7dd-311">`SignInAsync` 创建一个加密的 cookie，并将其添加到当前响应。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-311">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="ac7dd-312">如果未指定`AuthenticationScheme`，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-312">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="ac7dd-313">事实上，使用的加密是 ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection#security-data-protection-getting-started)系统。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-313">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="ac7dd-314">如果托管应用在多台计算机、 负载平衡跨应用程序，或使用 web 场，则必须[配置数据保护](xref:security/data-protection/configuration/overview)使用相同密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-314">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="ac7dd-315">注销</span><span class="sxs-lookup"><span data-stu-id="ac7dd-315">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ac7dd-316">若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="ac7dd-316">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ac7dd-317">若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="ac7dd-317">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="ac7dd-318">如果不使用`CookieAuthenticationDefaults.AuthenticationScheme`（或"Cookie"） 作为方案 (例如，"ContosoCookie")，提供配置身份验证提供程序时使用的方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-318">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="ac7dd-319">否则，使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-319">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="ac7dd-320">对后端更改做出响应</span><span class="sxs-lookup"><span data-stu-id="ac7dd-320">React to back-end changes</span></span>

<span data-ttu-id="ac7dd-321">一旦创建了 cookie，它将成为标识的单个来源。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-321">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="ac7dd-322">即使在后端系统中禁用用户，cookie 身份验证系统不了解，并且用户保持登录状态，只要其 cookie 有效。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-322">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="ac7dd-323">[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal)中 ASP.NET Core 事件 2.x 或[ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1)中 ASP.NET Core 1.x 可用来截获和重写的 cookie 身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-323">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="ac7dd-324">此方法可减轻已吊销的用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-324">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="ac7dd-325">Cookie 验证的一种方法取决于跟踪的更改用户数据库时。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-325">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="ac7dd-326">如果数据库未已发生更改，因为发布用户的 cookie，则无需重新验证用户，如果其 cookie 仍然有效。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-326">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="ac7dd-327">若要实现此方案中，数据库中实现`IUserRepository`对于此示例中，存储`LastChanged`值。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-327">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="ac7dd-328">在数据库中，更新的任何用户时`LastChanged`值设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-328">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="ac7dd-329">为了使 cookie 无效时的数据库更改基于`LastChanged`值时，请创建与 cookie`LastChanged`包含当前声明`LastChanged`值位于数据库中：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-329">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="ac7dd-330">若要实现的替代`ValidatePrincipal`事件，编写一个方法具有以下签名在从派生类中[CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="ac7dd-330">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="ac7dd-331">示例如下所示：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-331">An example looks like the following:</span></span>

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

<span data-ttu-id="ac7dd-332">在中的 cookie 服务注册期间注册的事件实例`ConfigureServices`方法。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-332">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="ac7dd-333">提供的作用域内的服务注册你`CustomCookieAuthenticationEvents`类：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-333">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

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

<span data-ttu-id="ac7dd-334">若要实现的替代`ValidateAsync`事件，编写一个方法具有以下签名：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-334">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="ac7dd-335">ASP.NET Core 标识作为的一部分实现这一检查其[SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync)。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-335">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="ac7dd-336">示例如下所示：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-336">An example looks like the following:</span></span>

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

<span data-ttu-id="ac7dd-337">在中的 cookie 身份验证配置期间注册事件`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="ac7dd-337">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

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

<span data-ttu-id="ac7dd-338">请考虑在其中更新用户的名称的情况下&mdash;不会影响任何方式的安全决策。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-338">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="ac7dd-339">如果您需要非破坏性地更新用户主体，请调用`context.ReplacePrincipal`并设置`context.ShouldRenew`属性设置为`true`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-339">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ac7dd-340">此处所述的方法上的每个请求触发。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-340">The approach described here is triggered on every request.</span></span> <span data-ttu-id="ac7dd-341">这可能导致应用程序对较大性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-341">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="ac7dd-342">永久 cookie</span><span class="sxs-lookup"><span data-stu-id="ac7dd-342">Persistent cookies</span></span>

<span data-ttu-id="ac7dd-343">你可能想要在浏览器会话之间持久保存的 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-343">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="ac7dd-344">仅进行显式用户同意，有一个"记住我"复选框上登录名或类似机制，应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-344">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="ac7dd-345">下面的代码段创建的标识和相应可以幸存，但通过浏览器闭包的 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-345">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="ac7dd-346">遵循以前配置任何滑动过期设置。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-346">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="ac7dd-347">如果 cookie 已过期浏览器处于关闭状态时，浏览器中清除 cookie 后重新启动它。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-347">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

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

<span data-ttu-id="ac7dd-348">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0)类驻留在`Microsoft.AspNetCore.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-348">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

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

<span data-ttu-id="ac7dd-349">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1)类驻留在`Microsoft.AspNetCore.Http.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-349">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="ac7dd-350">绝对 cookie 到期时间</span><span class="sxs-lookup"><span data-stu-id="ac7dd-350">Absolute cookie expiration</span></span>

<span data-ttu-id="ac7dd-351">可以设置使用绝对到期时间`ExpiresUtc`。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-351">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="ac7dd-352">您还必须设置`IsPersistent`; 否则为`ExpiresUtc`将被忽略，并创建一个单会话 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-352">You must also set `IsPersistent`; otherwise, `ExpiresUtc` is ignored and a single-session cookie is created.</span></span> <span data-ttu-id="ac7dd-353">当`ExpiresUtc`上设置`SignInAsync`，它将重写的值`ExpireTimeSpan`的选项`CookieAuthenticationOptions`，如果设置。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-353">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="ac7dd-354">下面的代码段创建的标识和相应的 cookie 的持续时间为 20 分钟。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-354">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="ac7dd-355">这会忽略以前配置的任何滑动过期设置。</span><span class="sxs-lookup"><span data-stu-id="ac7dd-355">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ac7dd-356">其他资源</span><span class="sxs-lookup"><span data-stu-id="ac7dd-356">Additional resources</span></span>

* [<span data-ttu-id="ac7dd-357">身份验证 2.0 更改 / 迁移公告</span><span class="sxs-lookup"><span data-stu-id="ac7dd-357">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="ac7dd-358">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="ac7dd-358">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
