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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="320ac-103">保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌</span><span class="sxs-lookup"><span data-stu-id="320ac-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="320ac-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="320ac-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="320ac-105">ASP.NET Core 应用可以建立其他声明和来自外部身份验证提供程序，如 Facebook、 Google、 Microsoft 和 Twitter 令牌。</span><span class="sxs-lookup"><span data-stu-id="320ac-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="320ac-106">每个提供程序会显示不同用户的信息在其平台上，但接收并将用户数据转换成其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="320ac-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="320ac-107">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="320ac-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="320ac-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="320ac-108">Prerequisites</span></span>

<span data-ttu-id="320ac-109">决定哪些外部身份验证提供程序支持应用程序中。</span><span class="sxs-lookup"><span data-stu-id="320ac-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="320ac-110">对于每个提供程序，注册应用程序和获取客户端 ID 和客户端机密。</span><span class="sxs-lookup"><span data-stu-id="320ac-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="320ac-111">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="320ac-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="320ac-112">示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="320ac-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="320ac-113">设置客户端 ID 和客户端机密</span><span class="sxs-lookup"><span data-stu-id="320ac-113">Set the client ID and client secret</span></span>

<span data-ttu-id="320ac-114">OAuth 身份验证提供程序使用客户端 ID 和客户端机密的应用与建立信任关系。</span><span class="sxs-lookup"><span data-stu-id="320ac-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="320ac-115">客户端 ID 和客户端密钥值的应用的外部身份验证提供程序时创建应用注册到提供程序。</span><span class="sxs-lookup"><span data-stu-id="320ac-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="320ac-116">提供程序的客户端 ID 和客户端机密，必须单独配置每个应用程序使用的外部提供程序。</span><span class="sxs-lookup"><span data-stu-id="320ac-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="320ac-117">有关详细信息，请参阅适用于方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="320ac-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="320ac-118">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="320ac-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="320ac-119">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="320ac-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="320ac-120">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="320ac-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="320ac-121">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="320ac-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="320ac-122">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="320ac-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="320ac-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="320ac-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="320ac-124">示例应用程序使用客户端 ID 和 Google 提供的客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="320ac-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="320ac-125">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="320ac-125">Establish the authentication scope</span></span>

<span data-ttu-id="320ac-126">指定要从提供程序检索由指定的权限列表<xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>。</span><span class="sxs-lookup"><span data-stu-id="320ac-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="320ac-127">下表中显示常见的外部提供程序的身份验证作用域。</span><span class="sxs-lookup"><span data-stu-id="320ac-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="320ac-128">提供程序</span><span class="sxs-lookup"><span data-stu-id="320ac-128">Provider</span></span>  | <span data-ttu-id="320ac-129">范围</span><span class="sxs-lookup"><span data-stu-id="320ac-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="320ac-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="320ac-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="320ac-131">Google</span><span class="sxs-lookup"><span data-stu-id="320ac-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="320ac-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="320ac-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="320ac-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="320ac-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="320ac-134">在示例应用，Google`userinfo.profile`由框架自动添加作用域时<xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>上调用<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>。</span><span class="sxs-lookup"><span data-stu-id="320ac-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="320ac-135">如果应用需要其他作用域，则将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="320ac-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="320ac-136">在下面的示例中，Google`https://www.googleapis.com/auth/user.birthday.read`以检索用户的生日添加作用域：</span><span class="sxs-lookup"><span data-stu-id="320ac-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="320ac-137">将用户数据键映射和创建声明</span><span class="sxs-lookup"><span data-stu-id="320ac-137">Map user data keys and create claims</span></span>

<span data-ttu-id="320ac-138">在提供程序的选项中，指定<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>或<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>外部提供程序的应用标识登录上读取 JSON 用户数据中每个键/子项。</span><span class="sxs-lookup"><span data-stu-id="320ac-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="320ac-139">声明类型的详细信息，请参阅<xref:System.Security.Claims.ClaimTypes>。</span><span class="sxs-lookup"><span data-stu-id="320ac-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="320ac-140">示例应用创建区域设置 (`urn:google:locale`) 和图片 (`urn:google:picture`) 从声明`locale`和`picture`Google 用户数据中的密钥：</span><span class="sxs-lookup"><span data-stu-id="320ac-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="320ac-141">在中<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>、 一个<xref:Microsoft.AspNetCore.Identity.IdentityUser>(`ApplicationUser`) 登录到应用程序与<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>。</span><span class="sxs-lookup"><span data-stu-id="320ac-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="320ac-142">在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601>可以存储`ApplicationUser`从可用的用户数据的声明<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>。</span><span class="sxs-lookup"><span data-stu-id="320ac-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="320ac-143">在示例应用中， `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) 建立的区域设置 (`urn:google:locale`) 和图片 (`urn:google:picture`) 的有符号的声明中`ApplicationUser`，包括的声明<xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="320ac-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="320ac-144">默认情况下，身份验证 cookie 中存储用户的声明。</span><span class="sxs-lookup"><span data-stu-id="320ac-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="320ac-145">如果身份验证 cookie 而言太大，则可能导致应用失败，因为：</span><span class="sxs-lookup"><span data-stu-id="320ac-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="320ac-146">在浏览器检测到的 cookie 标头太长。</span><span class="sxs-lookup"><span data-stu-id="320ac-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="320ac-147">请求的总体大小来说太大。</span><span class="sxs-lookup"><span data-stu-id="320ac-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="320ac-148">如果用于处理用户请求需要大量的用户数据：</span><span class="sxs-lookup"><span data-stu-id="320ac-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="320ac-149">限制的数量和大小的请求到仅应用需要处理的用户声明。</span><span class="sxs-lookup"><span data-stu-id="320ac-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="320ac-150">使用自定义<xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>Cookie 身份验证中间件的<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore>跨请求存储的标识。</span><span class="sxs-lookup"><span data-stu-id="320ac-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="320ac-151">保留大量的服务器上仅向客户端发送一个小型的会话标识符密钥时的标识信息。</span><span class="sxs-lookup"><span data-stu-id="320ac-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="320ac-152">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="320ac-152">Save the access token</span></span>

<span data-ttu-id="320ac-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义访问和刷新令牌是否应存储在<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>成功授权后。</span><span class="sxs-lookup"><span data-stu-id="320ac-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="320ac-154">`SaveTokens` 设置为`false`默认情况下以减小最终的身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="320ac-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="320ac-155">示例应用程序设置的值`SaveTokens`到`true`中<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="320ac-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="320ac-156">当`OnPostConfirmationAsync`执行时，存储的访问令牌 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) 中的外部提供程序从`ApplicationUser`的`AuthenticationProperties`。</span><span class="sxs-lookup"><span data-stu-id="320ac-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="320ac-157">示例应用将保存中的访问令牌`OnPostConfirmationAsync`（新用户注册） 和`OnGetCallbackAsync`（以前已注册的用户） 在*Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="320ac-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="320ac-158">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="320ac-158">How to add additional custom tokens</span></span>

<span data-ttu-id="320ac-159">若要演示如何添加作为的一部分存储的自定义令牌`SaveTokens`，示例应用添加<xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>与当前<xref:System.DateTime>有关[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)的`TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="320ac-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="320ac-160">创建并添加声明</span><span class="sxs-lookup"><span data-stu-id="320ac-160">Creating and adding claims</span></span>

<span data-ttu-id="320ac-161">该框架提供常见的操作和用于创建和将声明添加到集合的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="320ac-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="320ac-162">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="320ac-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="320ac-163">用户可以通过从派生来定义自定义操作<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction>和实现抽象<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>方法。</span><span class="sxs-lookup"><span data-stu-id="320ac-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="320ac-164">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="320ac-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="320ac-165">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="320ac-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="320ac-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)移除所有声明的操作给定<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>集合中。</span><span class="sxs-lookup"><span data-stu-id="320ac-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="320ac-167">[ClaimActionCollectionMapExtensions.DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)删除一个声明的给定<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType>的标识。</span><span class="sxs-lookup"><span data-stu-id="320ac-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="320ac-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要用于工作[OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)删除协议生成声明。</span><span class="sxs-lookup"><span data-stu-id="320ac-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="320ac-169">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="320ac-169">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="320ac-170">其他资源</span><span class="sxs-lookup"><span data-stu-id="320ac-170">Additional resources</span></span>

* <span data-ttu-id="320ac-171">[aspnet/AspNetCore 工程 SocialSample 应用](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)&ndash;链接的示例应用是上[aspnet/AspNetCore GitHub 存储库](https://github.com/aspnet/AspNetCore)`master`工程分支。</span><span class="sxs-lookup"><span data-stu-id="320ac-171">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="320ac-172">`master`分支包含正在活动开发中的下一版本的 ASP.NET Core 的代码。</span><span class="sxs-lookup"><span data-stu-id="320ac-172">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="320ac-173">若要查看适用于 ASP.NET Core 的发行版本的示例应用的版本，请使用**分支**下拉列表中选择发布分支 (例如`release/2.2`)。</span><span class="sxs-lookup"><span data-stu-id="320ac-173">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/2.2`).</span></span>
