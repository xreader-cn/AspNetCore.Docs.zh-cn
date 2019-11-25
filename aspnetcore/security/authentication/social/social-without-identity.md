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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>在不 ASP.NET Core 标识的情况下使用社交登录提供程序身份验证

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。 该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。

此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。 这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。

此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。 使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。 若要与其他外部身份验证提供程序集成，请参阅以下主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他提供程序](xref:security/authentication/otherlogins)

## <a name="configuration"></a>配置

在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>和 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 方法配置应用的身份验证方案：

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

对的调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 设置应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>。 `DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。 如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。 `DefaultScheme``DefaultChallengeScheme` 重写。 有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。

在 `Startup.Configure`中，调用 `UseRouting` 和 `UseEndpoints`之间的 `UseAuthentication` 和 `UseAuthorization`。 这会设置 `HttpContext.User` 属性，并为请求运行授权中间件：

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。

## <a name="apply-authorization"></a>应用授权

通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。 以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。 下面的代码将 `Logout` 页处理程序添加到*索引*页：

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

请注意，对 `SignOutAsync` 的调用未指定身份验证方案。 应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> 介绍如何使用户能够使用 OAuth 2.0 通过外部身份验证提供程序中的凭据进行登录。 该主题中所述的方法包括 ASP.NET Core 标识作为身份验证提供程序。

此示例演示如何使用外部身份验证提供程序，**而无需**ASP.NET Core 标识。 这对于不需要 ASP.NET Core 标识的所有功能，但仍需要与受信任的外部身份验证提供程序集成的应用很有用。

此示例使用[Google 身份验证](xref:security/authentication/google-logins)对用户进行身份验证。 使用 Google 身份验证将管理登录过程的许多复杂性转移到 Google。 若要与其他外部身份验证提供程序集成，请参阅以下主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他提供程序](xref:security/authentication/otherlogins)

## <a name="configuration"></a>配置

在 `ConfigureServices` 方法中，使用 `AddAuthentication`、`AddCookie`和 `AddGoogle` 方法配置应用的身份验证方案：

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

对[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的调用将设置应用的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。 `DefaultScheme` 是以下 `HttpContext` 身份验证扩展方法使用的默认方案：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

如果将应用程序的 `DefaultScheme` 设置为[CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) （"cookie"），则会将应用程序配置为使用 cookie 作为这些扩展方法的默认方案。 如果将应用程序的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 设置为[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) （"Google"），则会将应用程序配置为使用 Google 作为调用 `ChallengeAsync`的默认方案。 `DefaultScheme``DefaultChallengeScheme` 重写。 有关在设置时覆盖 `DefaultScheme` 的其他属性，请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>。

在 `Configure` 方法中，调用 `UseAuthentication` 方法来调用设置 `HttpContext.User` 属性的身份验证中间件。 在调用 `UseAuthentication` 或 `UseMvcWithDefaultRoute` 之前调用 `UseMvc` 方法：

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

若要详细了解身份验证方案和 cookie 身份验证，请参阅 <xref:security/authentication/cookie>。

## <a name="apply-authorization"></a>应用授权

通过将 `AuthorizeAttribute` 特性应用到控制器、操作或页来测试应用的身份验证配置。 以下代码将访问*隐私*页面的权限限制为已经过身份验证的用户：

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>注销

若要注销当前用户并删除其 cookie，请调用[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。 下面的代码将 `Logout` 页处理程序添加到*索引*页：

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

请注意，对 `SignOutAsync` 的调用未指定身份验证方案。 应用程序的 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 用作回退。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
