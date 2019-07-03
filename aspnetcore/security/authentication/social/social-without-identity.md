---
title: Facebook、 Google 和外部提供程序而无需 ASP.NET Core 标识的身份验证
author: rick-anderson
description: 使用 Facebook、 Google、 Twitter，而无需 ASP.NET Core 标识等帐户用户身份验证的说明。
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: e67da513fef1ce453110c465b08e9c7965e71df5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557646"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>使用社交登录提供程序而无需 ASP.NET Core 标识的身份验证

<xref:security/authentication/social/index> 介绍如何让用户能够在 OAuth 2.0 中使用外部身份验证提供程序的凭据登录。 该主题中介绍的方法包括 ASP.NET Core 标识为身份验证提供程序。

此示例演示如何使用外部身份验证提供程序**而无需**ASP.NET Core 标识。 这可用于应用程序不需要的所有功能的 ASP.NET Core 标识，但仍需要与受信任的外部身份验证提供程序的集成。

此示例使用[Google 身份验证](xref:security/authentication/google-logins)用户进行身份验证。 使用 Google 身份验证会转移到 Google 登录过程的复杂问题的许多。 若要将与不同的外部身份验证提供程序集成，请参阅以下主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他提供程序](xref:security/authentication/otherlogins)

## <a name="configuration"></a>配置

在中`ConfigureServices`方法中，配置与应用程序的身份验证方案`AddAuthentication`，`AddCookie`和`AddGoogle`方法：

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

在调用[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)将应用设置[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。 `DefaultScheme`是默认架构，它由以下`HttpContext`身份验证的扩展方法：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

设置应用程序的`DefaultScheme`到[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookie") 配置的应用可使用这些扩展方法的默认方案作为 Cookie。 设置应用程序的<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>到[GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") 配置的应用可使用 Google 作为默认方案对的调用`ChallengeAsync`。 `DefaultChallengeScheme` 重写`DefaultScheme`。 请参阅<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>的其他属性，重写`DefaultScheme`时设置。

在中`Configure`方法中，调用`UseAuthentication`方法调用设置身份验证中间件`HttpContext.User`属性。 在调用 `UseMvcWithDefaultRoute` 或 `UseMvc` 之前调用 `UseAuthentication` 方法：

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

若要了解有关身份验证方案和 cookie 身份验证的详细信息，请参阅<xref:security/authentication/cookie>。

## <a name="applying-basic-authorization"></a>应用基本授权

通过应用中测试应用程序的身份验证配置`AuthorizeAttribute`控制器、 操作或页的属性。 下面的代码访问权限限制为*隐私*已经过身份验证的用户页：

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>注销

若要注销当前用户，然后删除其 cookie，调用[SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)。 下面的代码添加`Logout`页处理程序*索引*页：

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

请注意，在调用`SignOutAsync`不指定身份验证方案。 应用程序的`DefaultScheme`的`CookieAuthenticationDefaults.AuthenticationScheme`用作回退。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
