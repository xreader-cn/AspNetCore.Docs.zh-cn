---
title: 保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌
author: guardrex
description: 了解如何建立其他声明和来自外部提供程序的令牌。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 49c323fab64bd4ea52dd1d8cf2e43a79d4d0d0dc
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327347"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌

作者：[Luke Latham](https://github.com/guardrex)

ASP.NET Core 应用可以建立其他声明和来自外部身份验证提供程序，如 Facebook、 Google、 Microsoft 和 Twitter 令牌。 每个提供程序会显示不同用户的信息在其平台上，但接收并将用户数据转换成其他声明的模式是相同的。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

决定哪些外部身份验证提供程序支持应用程序中。 对于每个提供程序，注册应用程序和获取客户端 ID 和客户端机密。 有关详细信息，请参阅 <xref:security/authentication/social/index>。 [示例应用](#sample-app-instructions)使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。

## <a name="set-the-client-id-and-client-secret"></a>设置客户端 ID 和客户端机密

OAuth 身份验证提供程序使用客户端 ID 和客户端机密的应用与建立信任关系。 客户端 ID 和客户端密钥值的应用的外部身份验证提供程序时创建应用注册到提供程序。 提供程序的客户端 ID 和客户端机密，必须单独配置每个应用程序使用的外部提供程序。 有关详细信息，请参阅适用于方案的外部身份验证提供程序主题：

* [Facebook 身份验证](xref:security/authentication/facebook-logins)
* [Google 身份验证](xref:security/authentication/google-logins)
* [Microsoft 身份验证](xref:security/authentication/microsoft-logins)
* [Twitter 身份验证](xref:security/authentication/twitter-logins)
* [其他身份验证提供程序](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

示例应用程序使用客户端 ID 和 Google 提供的客户端机密配置 Google 身份验证提供程序：

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a>建立身份验证范围

指定要从提供程序检索由指定的权限列表<xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>。 下表中显示常见的外部提供程序的身份验证作用域。

| 提供程序  | 范围                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/plus.login`                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

该示例应用将添加 Google`plus.login`作用域以请求登录 Google + 中的权限：

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a>将用户数据键映射和创建声明

在提供程序的选项中，指定<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>要阅读在登录的应用程序标识的外部提供程序的 JSON 用户数据中的每个键。 声明类型的详细信息，请参阅<xref:System.Security.Claims.ClaimTypes>。

示例应用创建<xref:System.Security.Claims.ClaimTypes.Gender>将来自声明`gender`Google 用户数据中的键：

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

在中<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>、 一个<xref:Microsoft.AspNetCore.Identity.IdentityUser>(`ApplicationUser`) 登录到应用程序与<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>。 在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601>可以存储`ApplicationUser`声明的用户数据可从<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>。

在示例应用`OnPostConfirmationAsync`(*Account/ExternalLogin.cshtml.cs*) 建立<xref:System.Security.Claims.ClaimTypes.Gender>声明的签名中`ApplicationUser`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a>保存访问令牌

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义访问和刷新令牌是否应存储在<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>成功授权后。 `SaveTokens` 设置为`false`默认情况下以减小最终的身份验证 cookie 的大小。

示例应用程序设置的值`SaveTokens`到`true`中<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

当`OnPostConfirmationAsync`执行时，存储的访问令牌 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) 中的外部提供程序从`ApplicationUser`的`AuthenticationProperties`。

示例应用将保存中的访问令牌：

* `OnPostConfirmationAsync` &ndash; 执行新的用户注册。
* `OnGetCallbackAsync` &ndash; 在以前已注册的用户登录到应用时执行。

*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a>如何添加其他自定义令牌

若要演示如何添加作为的一部分存储的自定义令牌`SaveTokens`，示例应用添加<xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>与当前<xref:System.DateTime>有关[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)的`TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a>示例应用程序说明

示例应用演示了如何：

* 从 Google 获取用户的性别和存储的值的性别声明。
* 将 Google 访问令牌存储在用户的`AuthenticationProperties`。

若要使用示例应用程序：

1. 注册应用程序和获取有效的客户端 ID 和 Google 身份验证的客户端机密。 有关详细信息，请参阅 <xref:security/authentication/google-logins>。
1. 提供的客户端 ID 和客户端密钥对中的应用程序<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>的`Startup.ConfigureServices`。
1. 运行应用并请求我声明页。 如果用户未登录，该应用将重定向到 Google。 使用 Google 登录。 Google 将用户重定向回应用程序 (`/Home/MyClaims`)。 用户进行身份验证，并加载我的声明页。 不存在下性别声明**用户声明**使用从 Google 获取的值。 访问令牌显示在**身份验证属性**。

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
