---
title: Facebook、Google 和外部提供程序身份验证，无需 ASP.NET Core 标识
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等帐户用户身份验证的说明，无需 ASP.NET Core 标识。
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: b30ce7055b35b721c7fb83b61a328200d6a136b1
ms.sourcegitcommit: 3ca4a2235a8129def9e480d0a6ad54cc856920ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025397"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="004e8-103">在不 ASP.NET Core 标识的情况下使用社交登录提供程序身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="004e8-104">作者： [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="004e8-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="004e8-105"><xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="004e8-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="004e8-106">该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="004e8-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="004e8-107">此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="004e8-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="004e8-108">这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="004e8-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="004e8-109">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="004e8-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="004e8-110">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="004e8-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="004e8-111">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="004e8-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="004e8-112">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="004e8-113">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="004e8-114">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="004e8-115">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="004e8-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="004e8-116">Configuration</span><span class="sxs-lookup"><span data-stu-id="004e8-116">Configuration</span></span>

<span data-ttu-id="004e8-117">在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>和 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 方法配置应用的身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="004e8-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="004e8-118">对的调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 设置应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>。</span><span class="sxs-lookup"><span data-stu-id="004e8-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="004e8-119">`DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="004e8-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="004e8-120">如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="004e8-121">如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="004e8-122">`DefaultScheme``DefaultChallengeScheme` 重写。</span><span class="sxs-lookup"><span data-stu-id="004e8-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="004e8-123">有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。</span><span class="sxs-lookup"><span data-stu-id="004e8-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="004e8-124">在 `Startup.Configure`中，调用 `UseRouting` 和 `UseEndpoints`之间的 `UseAuthentication` 和 `UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="004e8-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="004e8-125">这会设置 `HttpContext.User` 属性，并为请求运行授权中间件：</span><span class="sxs-lookup"><span data-stu-id="004e8-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="004e8-126">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="004e8-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="004e8-127">若要详细了解 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。</span><span class="sxs-lookup"><span data-stu-id="004e8-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="004e8-128">应用授权</span><span class="sxs-lookup"><span data-stu-id="004e8-128">Apply authorization</span></span>

<span data-ttu-id="004e8-129">通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="004e8-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="004e8-130">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="004e8-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="004e8-131">注销</span><span class="sxs-lookup"><span data-stu-id="004e8-131">Sign out</span></span>

<span data-ttu-id="004e8-132">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="004e8-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="004e8-133">下面的代码将 `Logout` 页处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="004e8-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="004e8-134">请注意，对 `SignOutAsync` 的调用未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="004e8-135">应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。</span><span class="sxs-lookup"><span data-stu-id="004e8-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="004e8-136">其他资源</span><span class="sxs-lookup"><span data-stu-id="004e8-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="004e8-137"><xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。</span><span class="sxs-lookup"><span data-stu-id="004e8-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="004e8-138">该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="004e8-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="004e8-139">此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="004e8-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="004e8-140">这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。</span><span class="sxs-lookup"><span data-stu-id="004e8-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="004e8-141">此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="004e8-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="004e8-142">使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。</span><span class="sxs-lookup"><span data-stu-id="004e8-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="004e8-143">若要与其他外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="004e8-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="004e8-144">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="004e8-145">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="004e8-146">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="004e8-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="004e8-147">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="004e8-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="004e8-148">Configuration</span><span class="sxs-lookup"><span data-stu-id="004e8-148">Configuration</span></span>

<span data-ttu-id="004e8-149">在 `ConfigureServices` 方法中，使用 `AddAuthentication`、`AddCookie`和 `AddGoogle` 方法配置应用的身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="004e8-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="004e8-150">对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="004e8-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="004e8-151">`DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：</span><span class="sxs-lookup"><span data-stu-id="004e8-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="004e8-152">如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="004e8-153">如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="004e8-154">`DefaultScheme``DefaultChallengeScheme` 重写。</span><span class="sxs-lookup"><span data-stu-id="004e8-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="004e8-155">有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。</span><span class="sxs-lookup"><span data-stu-id="004e8-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="004e8-156">在 `Configure` 方法中，调用 `UseAuthentication` 方法来调用设置 `HttpContext.User` 属性的身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="004e8-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="004e8-157">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc`之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="004e8-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="004e8-158">若要详细了解身份验证方案，请参阅[身份验证概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="004e8-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="004e8-159">若要详细了解 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。</span><span class="sxs-lookup"><span data-stu-id="004e8-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="004e8-160">应用授权</span><span class="sxs-lookup"><span data-stu-id="004e8-160">Apply authorization</span></span>

<span data-ttu-id="004e8-161">通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="004e8-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="004e8-162">以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="004e8-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="004e8-163">注销</span><span class="sxs-lookup"><span data-stu-id="004e8-163">Sign out</span></span>

<span data-ttu-id="004e8-164">若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="004e8-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="004e8-165">下面的代码将 `Logout` 页处理程序添加到*索引*页：</span><span class="sxs-lookup"><span data-stu-id="004e8-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="004e8-166">请注意，对 `SignOutAsync` 的调用未指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="004e8-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="004e8-167">应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。</span><span class="sxs-lookup"><span data-stu-id="004e8-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="004e8-168">其他资源</span><span class="sxs-lookup"><span data-stu-id="004e8-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
