---
title: Facebook、Google 和外部提供程序身份验证，无需 ASP.NET Core 标识
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等帐户用户身份验证的说明，无需 ASP.NET Core 标识。
ms.author: riande
ms.date: 11/19/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 680ea091dcc5ed7f94879b5d277e8be7e5abeb7b
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289120"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="77c68-103">在不 ASP.NET Core 标识的情况下使用社交登录提供程序身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77c68-104"><xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="77c68-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="77c68-105">该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="77c68-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="77c68-106">此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="77c68-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="77c68-107">这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="77c68-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="77c68-108">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="77c68-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="77c68-109">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="77c68-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="77c68-110">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="77c68-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="77c68-111">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="77c68-112">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="77c68-113">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="77c68-114">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="77c68-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="77c68-115">配置</span><span class="sxs-lookup"><span data-stu-id="77c68-115">Configuration</span></span>

<span data-ttu-id="77c68-116">在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>和 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 方法配置应用的身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="77c68-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="77c68-117">对的调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 设置应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>。</span><span class="sxs-lookup"><span data-stu-id="77c68-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="77c68-118">`DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="77c68-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="77c68-119">如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="77c68-120">如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="77c68-121">`DefaultScheme``DefaultChallengeScheme` 重写。</span><span class="sxs-lookup"><span data-stu-id="77c68-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="77c68-122">有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。</span><span class="sxs-lookup"><span data-stu-id="77c68-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="77c68-123">在 `Startup.Configure`中，调用 `UseRouting` 和 `UseEndpoints`之间的 `UseAuthentication` 和 `UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="77c68-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="77c68-124">这会设置 `HttpContext.User` 属性，并为请求运行授权中间件：</span><span class="sxs-lookup"><span data-stu-id="77c68-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="77c68-125">若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。</span><span class="sxs-lookup"><span data-stu-id="77c68-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="77c68-126">应用授权</span><span class="sxs-lookup"><span data-stu-id="77c68-126">Apply authorization</span></span>

<span data-ttu-id="77c68-127">通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="77c68-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="77c68-128">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="77c68-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="77c68-129">注销</span><span class="sxs-lookup"><span data-stu-id="77c68-129">Sign out</span></span>

<span data-ttu-id="77c68-130">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="77c68-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="77c68-131">下面的代码将 `Logout` 页处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="77c68-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="77c68-132">请注意，对 `SignOutAsync` 的调用未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="77c68-133">应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。</span><span class="sxs-lookup"><span data-stu-id="77c68-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77c68-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="77c68-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77c68-135"><xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="77c68-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="77c68-136">该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="77c68-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="77c68-137">此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="77c68-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="77c68-138">这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="77c68-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="77c68-139">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="77c68-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="77c68-140">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="77c68-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="77c68-141">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="77c68-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="77c68-142">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="77c68-143">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="77c68-144">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="77c68-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="77c68-145">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="77c68-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="77c68-146">配置</span><span class="sxs-lookup"><span data-stu-id="77c68-146">Configuration</span></span>

<span data-ttu-id="77c68-147">在 `ConfigureServices` 方法中，使用 `AddAuthentication`、`AddCookie`和 `AddGoogle` 方法配置应用的身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="77c68-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="77c68-148">对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="77c68-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="77c68-149">`DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="77c68-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="77c68-150">如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="77c68-151">如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="77c68-152">`DefaultScheme``DefaultChallengeScheme` 重写。</span><span class="sxs-lookup"><span data-stu-id="77c68-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="77c68-153">有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。</span><span class="sxs-lookup"><span data-stu-id="77c68-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="77c68-154">在 `Configure` 方法中，调用 `UseAuthentication` 方法来调用设置 `HttpContext.User` 属性的身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="77c68-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="77c68-155">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc`之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="77c68-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="77c68-156">若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。</span><span class="sxs-lookup"><span data-stu-id="77c68-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="77c68-157">应用授权</span><span class="sxs-lookup"><span data-stu-id="77c68-157">Apply authorization</span></span>

<span data-ttu-id="77c68-158">通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="77c68-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="77c68-159">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="77c68-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="77c68-160">注销</span><span class="sxs-lookup"><span data-stu-id="77c68-160">Sign out</span></span>

<span data-ttu-id="77c68-161">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="77c68-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="77c68-162">下面的代码将 `Logout` 页处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="77c68-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="77c68-163">请注意，对 `SignOutAsync` 的调用未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="77c68-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="77c68-164">应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。</span><span class="sxs-lookup"><span data-stu-id="77c68-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77c68-165">其他资源</span><span class="sxs-lookup"><span data-stu-id="77c68-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
