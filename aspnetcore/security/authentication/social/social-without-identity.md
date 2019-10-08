---
title: Facebook、Google 和外部提供程序身份验证，无需 ASP.NET Core 标识
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等帐户用户身份验证的说明，无需 ASP.NET Core 标识。
ms.author: riande
ms.date: 09/25/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 54dd93a13b2f7ed09c2c305f529d5f4610567184
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999894"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>在不 ASP.NET Core 标识的情况下使用社交登录提供程序身份验证

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> 描述如何允许用户使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。 该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。

此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。 这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。

此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。 使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。 若要与其他外部身份验证提供程序集成，请参阅以下主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他提供程序](xref:security/authentication/otherlogins)

## <a name="configuration"></a>配置

在 `ConfigureServices` 方法中，配置应用的身份验证方案，其 @no__t 为-1、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 和 @no__t 3 方法：

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet1)]

对 @no__t 的调用会将应用的 @no__t 设置为-1。 @No__t 为以下 @no__t 身份验证扩展方法使用的默认方案：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。 如果将应用的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用配置为使用 Google 作为调用 @no__t 2 的默认方案。 `DefaultChallengeScheme` 重写 `DefaultScheme`。 有关设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。

在 `Startup.Configure` 中，调用 `UseAuthentication`，并 `UseAuthorization` 设置 @no__t 属性，并为请求运行授权中间件。 调用 `UseEndpoints` 之前调用 @no__t 0 和 @no__t 方法：

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet2)]

若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。

## <a name="apply-authorization"></a>应用授权

通过将 `AuthorizeAttribute` 属性应用于控制器、操作或页面来测试应用的身份验证配置。 以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：

[!code-csharp[](social-without-identity/3.0sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。 下面的代码将 @no__t 0 页面处理程序添加到*索引*页：

[!code-csharp[](social-without-identity/3.0sample/Pages/Index.cshtml.cs?name=snippet&highlight=14-18)]

请注意，对 @no__t 的调用未指定身份验证方案。 应用的 `DefaultScheme` 的 @no__t 用作回退。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> 描述如何允许用户使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。 该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。

此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。 这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。

此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。 使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。 若要与其他外部身份验证提供程序集成，请参阅以下主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他提供程序](xref:security/authentication/otherlogins)

## <a name="configuration"></a>配置

在 `ConfigureServices` 方法中，配置应用的身份验证方案，其 @no__t 为-1、`AddCookie` 和 @no__t 3 方法：

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。 @No__t 为以下 @no__t 身份验证扩展方法使用的默认方案：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。 如果将应用的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用配置为使用 Google 作为调用 @no__t 2 的默认方案。 `DefaultChallengeScheme` 重写 `DefaultScheme`。 有关设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。

在 `Configure` 方法中，调用 `UseAuthentication` 方法来调用设置 `HttpContext.User` 属性的身份验证中间件。 在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。

## <a name="apply-authorization"></a>应用授权

通过将 `AuthorizeAttribute` 属性应用于控制器、操作或页面来测试应用的身份验证配置。 以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。 下面的代码将 @no__t 0 页面处理程序添加到*索引*页：

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

请注意，对 @no__t 的调用未指定身份验证方案。 应用的 `DefaultScheme` 的 @no__t 用作回退。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
