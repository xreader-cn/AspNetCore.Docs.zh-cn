---
title: 无 ASP.NET Core 的 Facebook、Google 和外部提供程序身份验证Identity
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等帐户用户身份验证的说明（不 ASP.NET Core） Identity 。
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406038"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="5b31a-103">在不 ASP.NET Core 的情况下使用社交登录提供程序身份验证Identity</span><span class="sxs-lookup"><span data-stu-id="5b31a-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="5b31a-104">作者： [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5b31a-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b31a-105"><xref:security/authentication/social/index>描述如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="5b31a-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5b31a-106">该主题中所述的方法包括 ASP.NET Core Identity 身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="5b31a-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5b31a-107">此示例演示如何在不 ASP.NET Core 的**情况下**使用外部身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5b31a-108">这对于不需要 ASP.NET Core 的所有功能 Identity ，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="5b31a-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5b31a-109">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5b31a-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5b31a-110">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="5b31a-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5b31a-111">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="5b31a-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5b31a-112">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5b31a-113">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5b31a-114">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5b31a-115">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="5b31a-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5b31a-116">配置</span><span class="sxs-lookup"><span data-stu-id="5b31a-116">Configuration</span></span>

<span data-ttu-id="5b31a-117">在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 、 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 和方法配置应用的身份验证方案 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ：</span><span class="sxs-lookup"><span data-stu-id="5b31a-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5b31a-118">用于 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 设置应用的的调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="5b31a-119">`DefaultScheme`是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="5b31a-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5b31a-120">如果将应用程序的设置 `DefaultScheme` 为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="5b31a-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5b31a-121">如果将应用的设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用配置为使用 Google 作为调用的默认方案 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5b31a-122">`DefaultChallengeScheme`重写 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5b31a-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>有关设置时重写的其他属性，请参阅 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5b31a-124">在中 `Startup.Configure` ， `UseAuthentication` 在 `UseAuthorization` 调用和之间调用和 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="5b31a-125">这会设置 `HttpContext.User` 属性并为请求运行授权中间件：</span><span class="sxs-lookup"><span data-stu-id="5b31a-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="5b31a-126">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="5b31a-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5b31a-127">若要详细了解 cookie 身份验证，请参阅 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5b31a-128">应用授权</span><span class="sxs-lookup"><span data-stu-id="5b31a-128">Apply authorization</span></span>

<span data-ttu-id="5b31a-129">通过将 `AuthorizeAttribute` 特性应用于控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="5b31a-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5b31a-130">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="5b31a-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5b31a-131">注销</span><span class="sxs-lookup"><span data-stu-id="5b31a-131">Sign out</span></span>

<span data-ttu-id="5b31a-132">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="5b31a-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5b31a-133">下面的代码将 `Logout` 页面处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="5b31a-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5b31a-134">请注意，对的调用 `SignOutAsync` 未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5b31a-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5b31a-135">的应用程序 `DefaultScheme` 用作 `CookieAuthenticationDefaults.AuthenticationScheme` 回退。</span><span class="sxs-lookup"><span data-stu-id="5b31a-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b31a-136">其他资源</span><span class="sxs-lookup"><span data-stu-id="5b31a-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b31a-137"><xref:security/authentication/social/index>描述如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="5b31a-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5b31a-138">该主题中所述的方法包括 ASP.NET Core Identity 身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="5b31a-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5b31a-139">此示例演示如何在不 ASP.NET Core 的**情况下**使用外部身份验证提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5b31a-140">这对于不需要 ASP.NET Core 的所有功能 Identity ，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="5b31a-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5b31a-141">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5b31a-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5b31a-142">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="5b31a-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5b31a-143">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="5b31a-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5b31a-144">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5b31a-145">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5b31a-146">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="5b31a-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5b31a-147">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="5b31a-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5b31a-148">配置</span><span class="sxs-lookup"><span data-stu-id="5b31a-148">Configuration</span></span>

<span data-ttu-id="5b31a-149">在 `ConfigureServices` 方法中，使用 `AddAuthentication` 、 `AddCookie` 和方法配置应用的身份验证方案 `AddGoogle` ：</span><span class="sxs-lookup"><span data-stu-id="5b31a-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5b31a-150">对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="5b31a-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="5b31a-151">`DefaultScheme`是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="5b31a-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5b31a-152">如果将应用程序的设置 `DefaultScheme` 为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="5b31a-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5b31a-153">如果将应用的设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用配置为使用 Google 作为调用的默认方案 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5b31a-154">`DefaultChallengeScheme`重写 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5b31a-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>有关设置时重写的其他属性，请参阅 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5b31a-156">在 `Configure` 方法中，调用 `UseAuthentication` 方法以调用设置属性的身份验证中间件 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5b31a-157">`UseAuthentication`在调用或之前调用 `UseMvcWithDefaultRoute` 方法 `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="5b31a-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="5b31a-158">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="5b31a-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5b31a-159">若要详细了解 cookie 身份验证，请参阅 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="5b31a-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5b31a-160">应用授权</span><span class="sxs-lookup"><span data-stu-id="5b31a-160">Apply authorization</span></span>

<span data-ttu-id="5b31a-161">通过将 `AuthorizeAttribute` 特性应用于控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="5b31a-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5b31a-162">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="5b31a-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5b31a-163">注销</span><span class="sxs-lookup"><span data-stu-id="5b31a-163">Sign out</span></span>

<span data-ttu-id="5b31a-164">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="5b31a-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5b31a-165">下面的代码将 `Logout` 页面处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="5b31a-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5b31a-166">请注意，对的调用 `SignOutAsync` 未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5b31a-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5b31a-167">的应用程序 `DefaultScheme` 用作 `CookieAuthenticationDefaults.AuthenticationScheme` 回退。</span><span class="sxs-lookup"><span data-stu-id="5b31a-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b31a-168">其他资源</span><span class="sxs-lookup"><span data-stu-id="5b31a-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
