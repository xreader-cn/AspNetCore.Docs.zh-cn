---
title: 无 ASP.NET Core 的 Facebook、Google 和外部提供程序身份验证Identity
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等帐户用户身份验证的说明（不 ASP.NET Core） Identity 。
ms.author: riande
ms.date: 12/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 73055a262ac69c0fd6a7f59e77d23121e71ea3dd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021661"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-no-locidentity"></a><span data-ttu-id="696f1-103">在不 ASP.NET Core 的情况下使用社交登录提供程序身份验证Identity</span><span class="sxs-lookup"><span data-stu-id="696f1-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="696f1-104">作者： [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="696f1-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="696f1-105"><xref:security/authentication/social/index>描述如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="696f1-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="696f1-106">该主题中所述的方法包括 ASP.NET Core Identity 身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="696f1-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="696f1-107">此示例演示如何在不 ASP.NET Core 的**情况下**使用外部身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="696f1-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="696f1-108">这对于不需要 ASP.NET Core 的所有功能 Identity ，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="696f1-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="696f1-109">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="696f1-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="696f1-110">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="696f1-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="696f1-111">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="696f1-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="696f1-112">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="696f1-113">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="696f1-114">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="696f1-115">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="696f1-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="696f1-116">配置</span><span class="sxs-lookup"><span data-stu-id="696f1-116">Configuration</span></span>

<span data-ttu-id="696f1-117">在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 、 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 和方法配置应用的身份验证方案 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ：</span><span class="sxs-lookup"><span data-stu-id="696f1-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="696f1-118">用于 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 设置应用的的调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> 。</span><span class="sxs-lookup"><span data-stu-id="696f1-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="696f1-119">`DefaultScheme`是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="696f1-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="696f1-120">将应用的设置 `DefaultScheme` 为[ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ( " Cookie s" ) 将应用配置为使用 Cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="696f1-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="696f1-121">将应用的设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ( "google" ) 将应用配置为使用 Google 作为调用的默认方案 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="696f1-122">`DefaultChallengeScheme`重写 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="696f1-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>有关设置时重写的其他属性，请参阅 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="696f1-124">在中 `Startup.Configure` ， `UseAuthentication` 在 `UseAuthorization` 调用和之间调用和 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="696f1-125">这会设置 `HttpContext.User` 属性并为请求运行授权中间件：</span><span class="sxs-lookup"><span data-stu-id="696f1-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="696f1-126">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="696f1-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="696f1-127">若要了解有关 cookie 身份验证的详细信息，请参阅 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="696f1-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="696f1-128">应用授权</span><span class="sxs-lookup"><span data-stu-id="696f1-128">Apply authorization</span></span>

<span data-ttu-id="696f1-129">通过将 `AuthorizeAttribute` 特性应用于控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="696f1-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="696f1-130">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="696f1-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="696f1-131">注销</span><span class="sxs-lookup"><span data-stu-id="696f1-131">Sign out</span></span>

<span data-ttu-id="696f1-132">若要注销当前用户并将其删除 cookie ，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="696f1-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="696f1-133">下面的代码将 `Logout` 页面处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="696f1-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="696f1-134">请注意，对的调用 `SignOutAsync` 未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="696f1-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="696f1-135">的应用程序 `DefaultScheme` 用作 `CookieAuthenticationDefaults.AuthenticationScheme` 回退。</span><span class="sxs-lookup"><span data-stu-id="696f1-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="696f1-136">其他资源</span><span class="sxs-lookup"><span data-stu-id="696f1-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="696f1-137"><xref:security/authentication/social/index>描述如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="696f1-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="696f1-138">该主题中所述的方法包括 ASP.NET Core Identity 身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="696f1-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="696f1-139">此示例演示如何在不 ASP.NET Core 的**情况下**使用外部身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="696f1-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="696f1-140">这对于不需要 ASP.NET Core 的所有功能 Identity ，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="696f1-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="696f1-141">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="696f1-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="696f1-142">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="696f1-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="696f1-143">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="696f1-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="696f1-144">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="696f1-145">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="696f1-146">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="696f1-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="696f1-147">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="696f1-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="696f1-148">配置</span><span class="sxs-lookup"><span data-stu-id="696f1-148">Configuration</span></span>

<span data-ttu-id="696f1-149">在 `ConfigureServices` 方法中，使用 `AddAuthentication` 、 `AddCookie` 和方法配置应用的身份验证方案 `AddGoogle` ：</span><span class="sxs-lookup"><span data-stu-id="696f1-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="696f1-150">对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="696f1-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="696f1-151">`DefaultScheme`是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="696f1-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="696f1-152">将应用的设置 `DefaultScheme` 为[ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ( " Cookie s" ) 将应用配置为使用 Cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="696f1-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="696f1-153">将应用的设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ( "google" ) 将应用配置为使用 Google 作为调用的默认方案 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="696f1-154">`DefaultChallengeScheme`重写 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="696f1-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>有关设置时重写的其他属性，请参阅 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="696f1-156">在 `Configure` 方法中，调用 `UseAuthentication` 方法以调用设置属性的身份验证中间件 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="696f1-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="696f1-157">`UseAuthentication`在调用或之前调用 `UseMvcWithDefaultRoute` 方法 `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="696f1-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="696f1-158">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="696f1-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="696f1-159">若要了解有关 cookie 身份验证的详细信息，请参阅 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="696f1-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="696f1-160">应用授权</span><span class="sxs-lookup"><span data-stu-id="696f1-160">Apply authorization</span></span>

<span data-ttu-id="696f1-161">通过将 `AuthorizeAttribute` 特性应用于控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="696f1-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="696f1-162">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="696f1-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="696f1-163">注销</span><span class="sxs-lookup"><span data-stu-id="696f1-163">Sign out</span></span>

<span data-ttu-id="696f1-164">若要注销当前用户并将其删除 cookie ，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="696f1-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="696f1-165">下面的代码将 `Logout` 页面处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="696f1-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="696f1-166">请注意，对的调用 `SignOutAsync` 未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="696f1-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="696f1-167">的应用程序 `DefaultScheme` 用作 `CookieAuthenticationDefaults.AuthenticationScheme` 回退。</span><span class="sxs-lookup"><span data-stu-id="696f1-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="696f1-168">其他资源</span><span class="sxs-lookup"><span data-stu-id="696f1-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
