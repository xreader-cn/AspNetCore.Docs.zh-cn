---
title: Facebook、 Google 和外部提供程序而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 使用 Facebook、 Google、 Twitter，而无需 ASP.NET Core 标识等帐户用户身份验证的说明。
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 1e7124e8b07c0faf2d005ec3ef55c0414a697d64
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561572"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="e580f-103">使用社交登录提供程序而无需 ASP.NET Core 标识的身份验证</span><span class="sxs-lookup"><span data-stu-id="e580f-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e580f-104"><xref:security/authentication/social/index> 介绍如何让用户能够在 OAuth 2.0 中使用外部身份验证提供程序的凭据登录。</span><span class="sxs-lookup"><span data-stu-id="e580f-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="e580f-105">该主题中介绍的方法包括 ASP.NET Core 标识为身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="e580f-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="e580f-106">此示例演示如何使用外部身份验证提供程序**而无需**ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="e580f-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="e580f-107">这可用于应用程序不需要的所有功能的 ASP.NET Core 标识，但仍需要与受信任的外部身份验证提供程序的集成。</span><span class="sxs-lookup"><span data-stu-id="e580f-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="e580f-108">此示例使用[Google 身份验证](xref:security/authentication/google-logins)用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e580f-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="e580f-109">使用 Google 身份验证会转移到 Google 登录过程的复杂问题的许多。</span><span class="sxs-lookup"><span data-stu-id="e580f-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="e580f-110">若要将与不同的外部身份验证提供程序集成，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e580f-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="e580f-111">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="e580f-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="e580f-112">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="e580f-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="e580f-113">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="e580f-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="e580f-114">其他提供程序</span><span class="sxs-lookup"><span data-stu-id="e580f-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="e580f-115">配置</span><span class="sxs-lookup"><span data-stu-id="e580f-115">Configuration</span></span>

<span data-ttu-id="e580f-116">在中`ConfigureServices`方法中，配置与应用程序的身份验证方案`AddAuthentication`，`AddCookie`和`AddGoogle`方法：</span><span class="sxs-lookup"><span data-stu-id="e580f-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e580f-117">在调用[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)将应用设置[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="e580f-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="e580f-118">`DefaultScheme`是默认架构，它由以下`HttpContext`身份验证的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="e580f-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="e580f-119">设置应用程序的`DefaultScheme`到[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookie") 配置的应用可使用这些扩展方法的默认方案作为 Cookie。</span><span class="sxs-lookup"><span data-stu-id="e580f-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="e580f-120">设置应用程序的<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>到[GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") 配置的应用可使用 Google 作为默认方案对的调用`ChallengeAsync`。</span><span class="sxs-lookup"><span data-stu-id="e580f-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="e580f-121">`DefaultChallengeScheme` 重写`DefaultScheme`。</span><span class="sxs-lookup"><span data-stu-id="e580f-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="e580f-122">请参阅<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>的其他属性，重写`DefaultScheme`时设置。</span><span class="sxs-lookup"><span data-stu-id="e580f-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="e580f-123">在中`Configure`方法中，调用`UseAuthentication`方法调用设置身份验证中间件`HttpContext.User`属性。</span><span class="sxs-lookup"><span data-stu-id="e580f-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e580f-124">在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：</span><span class="sxs-lookup"><span data-stu-id="e580f-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e580f-125">若要了解有关身份验证方案和 cookie 身份验证的详细信息，请参阅<xref:security/authentication/cookie>。</span><span class="sxs-lookup"><span data-stu-id="e580f-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-authorization"></a><span data-ttu-id="e580f-126">应用授权</span><span class="sxs-lookup"><span data-stu-id="e580f-126">Applying authorization</span></span>

<span data-ttu-id="e580f-127">通过应用中测试应用程序的身份验证配置`AuthorizeAttribute`控制器、 操作或页的属性。</span><span class="sxs-lookup"><span data-stu-id="e580f-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="e580f-128">下面的代码访问权限限制为*隐私*已经过身份验证的用户页：</span><span class="sxs-lookup"><span data-stu-id="e580f-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="e580f-129">注销</span><span class="sxs-lookup"><span data-stu-id="e580f-129">Sign out</span></span>

<span data-ttu-id="e580f-130">若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="e580f-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="e580f-131">下面的代码添加`Logout`页处理程序*索引*页：</span><span class="sxs-lookup"><span data-stu-id="e580f-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="e580f-132">请注意，在调用`SignOutAsync`不指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e580f-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="e580f-133">应用程序的`DefaultScheme`的`CookieAuthenticationDefaults.AuthenticationScheme`用作回退。</span><span class="sxs-lookup"><span data-stu-id="e580f-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e580f-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="e580f-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
