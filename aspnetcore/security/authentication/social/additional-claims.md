---
title: 保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌
author: guardrex
description: 了解如何建立其他声明和来自外部提供程序的令牌。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874845"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌

作者：[Luke Latham](https://github.com/guardrex)

ASP.NET Core 应用可以建立其他声明和来自外部身份验证提供程序，如 Facebook、 Google、 Microsoft 和 Twitter 令牌。 每个提供程序会显示不同用户的信息在其平台上，但接收并将用户数据转换成其他声明的模式是相同的。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

决定哪些外部身份验证提供程序支持应用程序中。 对于每个提供程序，注册应用程序和获取客户端 ID 和客户端机密。 有关详细信息，请参阅 <xref:security/authentication/social/index>。 示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。

## <a name="set-the-client-id-and-client-secret"></a>设置客户端 ID 和客户端机密

OAuth 身份验证提供程序使用客户端 ID 和客户端机密的应用与建立信任关系。 客户端 ID 和客户端密钥值的应用的外部身份验证提供程序时创建应用注册到提供程序。 提供程序的客户端 ID 和客户端机密，必须单独配置每个应用程序使用的外部提供程序。 有关详细信息，请参阅适用于方案的外部身份验证提供程序主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Google 身份验证](xref:security/authentication/google-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他身份验证提供程序](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

示例应用程序使用客户端 ID 和 Google 提供的客户端机密配置 Google 身份验证提供程序：

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>建立身份验证范围

指定要从提供程序检索由指定的权限列表<xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>。 下表中显示常见的外部提供程序的身份验证作用域。

| 提供程序  | 范围                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

在示例应用，Google`userinfo.profile`由框架自动添加作用域时<xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>上调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>。 如果应用需要其他作用域，则将它们添加到选项。 在下面的示例中，Google`https://www.googleapis.com/auth/user.birthday.read`以检索用户的生日添加作用域：

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>将用户数据键映射和创建声明

在提供程序的选项中，指定<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>或<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>外部提供程序的应用标识登录上读取 JSON 用户数据中每个键/子项。 声明类型的详细信息，请参阅<xref:System.Security.Claims.ClaimTypes>。

示例应用创建区域设置 (`urn:google:locale`) 和图片 (`urn:google:picture`) 从声明`locale`和`picture`Google 用户数据中的密钥：

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

在中<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>、 一个<xref:Microsoft.AspNetCore.Identity.IdentityUser>(`ApplicationUser`) 登录到应用程序与<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>。 在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601>可以存储`ApplicationUser`从可用的用户数据的声明<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>。

在示例应用中， `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) 建立的区域设置 (`urn:google:locale`) 和图片 (`urn:google:picture`) 的有符号的声明中`ApplicationUser`，包括的声明<xref:System.Security.Claims.ClaimTypes.GivenName>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

默认情况下，身份验证 cookie 中存储用户的声明。 如果身份验证 cookie 而言太大，则可能导致应用失败，因为：

* 在浏览器检测到的 cookie 标头太长。
* 请求的总体大小来说太大。

如果用于处理用户请求需要大量的用户数据：

* 限制的数量和大小的请求到仅应用需要处理的用户声明。
* 使用自定义<xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>Cookie 身份验证中间件的<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>跨请求存储的标识。 保留大量的服务器上仅向客户端发送一个小型的会话标识符密钥时的标识信息。

## <a name="save-the-access-token"></a>保存访问令牌

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义访问和刷新令牌是否应存储在<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>成功授权后。 `SaveTokens` 设置为`false`默认情况下以减小最终的身份验证 cookie 的大小。

示例应用程序设置的值`SaveTokens`到`true`中<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

当`OnPostConfirmationAsync`执行时，存储的访问令牌 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) 中的外部提供程序从`ApplicationUser`的`AuthenticationProperties`。

示例应用将保存中的访问令牌`OnPostConfirmationAsync`（新用户注册） 和`OnGetCallbackAsync`（以前已注册的用户） 在*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>如何添加其他自定义令牌

若要演示如何添加作为的一部分存储的自定义令牌`SaveTokens`，示例应用添加<xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>与当前<xref:System.DateTime>有关[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)的`TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a>创建并添加声明

该框架提供常见的操作和用于创建和将声明添加到集合的扩展方法。 有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。

用户可以通过从派生来定义自定义操作<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>和实现抽象<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>方法。

有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。

## <a name="removal-of-claim-actions-and-claims"></a>删除声明操作和声明

[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)移除所有声明的操作给定<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>集合中。 [ClaimActionCollectionMapExtensions.DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)删除一个声明的给定<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>的标识。 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要用于工作[OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)删除协议生成声明。

## <a name="sample-app-output"></a>示例应用程序输出

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="additional-resources"></a>其他资源

* [aspnet/AspNetCore 工程 SocialSample 应用](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)&ndash;链接的示例应用是上[aspnet/AspNetCore GitHub 存储库](https://github.com/aspnet/AspNetCore)`master`工程分支。 `master`分支包含正在活动开发中的下一版本的 ASP.NET Core 的代码。 若要查看适用于 ASP.NET Core 的发行版本的示例应用的版本，请使用**分支**下拉列表中选择发布分支 (例如`release/2.2`)。
