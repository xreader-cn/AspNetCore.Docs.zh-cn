---
title: 在 ASP.NET Core 中保存外部提供程序的其他声明和令牌
author: rick-anderson
description: 了解如何建立来自外部提供程序的其他声明和标记。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 9dfe5745125e34ed813d078529471a0ba2a53ab0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654660"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="82557-103">在 ASP.NET Core 中保存外部提供程序的其他声明和令牌</span><span class="sxs-lookup"><span data-stu-id="82557-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82557-104">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="82557-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="82557-105">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="82557-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="82557-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82557-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82557-107">必备条件</span><span class="sxs-lookup"><span data-stu-id="82557-107">Prerequisites</span></span>

<span data-ttu-id="82557-108">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="82557-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="82557-109">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="82557-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="82557-110">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="82557-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="82557-111">示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="82557-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="82557-112">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="82557-112">Set the client ID and client secret</span></span>

<span data-ttu-id="82557-113">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="82557-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="82557-114">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="82557-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="82557-115">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="82557-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="82557-116">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="82557-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="82557-117">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="82557-118">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="82557-119">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="82557-120">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="82557-121">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="82557-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="82557-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="82557-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="82557-123">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="82557-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="82557-124">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="82557-124">Establish the authentication scope</span></span>

<span data-ttu-id="82557-125">通过指定 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>指定要从提供程序检索的权限的列表。</span><span class="sxs-lookup"><span data-stu-id="82557-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="82557-126">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="82557-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="82557-127">提供程序</span><span class="sxs-lookup"><span data-stu-id="82557-127">Provider</span></span>  | <span data-ttu-id="82557-128">范围</span><span class="sxs-lookup"><span data-stu-id="82557-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="82557-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="82557-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="82557-130">Google</span><span class="sxs-lookup"><span data-stu-id="82557-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="82557-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="82557-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="82557-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="82557-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="82557-133">在示例应用中，当对 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>调用 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 时，框架会自动添加 Google 的 `userinfo.profile` 范围。</span><span class="sxs-lookup"><span data-stu-id="82557-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="82557-134">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="82557-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="82557-135">在下面的示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以便检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="82557-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="82557-136">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="82557-136">Map user data keys and create claims</span></span>

<span data-ttu-id="82557-137">在提供程序的选项中，为外部提供程序的 JSON 用户数据中的每个键/子项指定 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 或 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="82557-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="82557-138">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes>。</span><span class="sxs-lookup"><span data-stu-id="82557-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="82557-139">该示例应用在 Google 用户数据中的 `locale` 和 `picture` 密钥中创建区域设置（`urn:google:locale`）和图片（`urn:google:picture`）声明：</span><span class="sxs-lookup"><span data-stu-id="82557-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="82557-140">在 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`中，<xref:Microsoft.AspNetCore.Identity.IdentityUser> （`ApplicationUser`）使用 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>登录到应用中。</span><span class="sxs-lookup"><span data-stu-id="82557-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="82557-141">在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以存储 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>提供的用户数据的 `ApplicationUser` 声明。</span><span class="sxs-lookup"><span data-stu-id="82557-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="82557-142">在示例应用中，`OnPostConfirmationAsync` （*Account/ExternalLogin*）为已登录的 `ApplicationUser`建立了区域设置（`urn:google:locale`）和图片（`urn:google:picture`）声明，其中包括 <xref:System.Security.Claims.ClaimTypes.GivenName>的声明：</span><span class="sxs-lookup"><span data-stu-id="82557-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="82557-143">默认情况下，用户的声明存储在身份验证 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="82557-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="82557-144">如果身份验证 cookie 太大，则可能会导致应用程序失败，因为：</span><span class="sxs-lookup"><span data-stu-id="82557-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="82557-145">浏览器检测到 cookie 标头太长。</span><span class="sxs-lookup"><span data-stu-id="82557-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="82557-146">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="82557-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="82557-147">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="82557-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="82557-148">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="82557-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="82557-149">使用 Cookie 身份验证中间件 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 的自定义 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="82557-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="82557-150">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="82557-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="82557-151">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="82557-151">Save the access token</span></span>

<span data-ttu-id="82557-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义在授权成功后是否应将访问令牌和刷新令牌存储到 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 中。</span><span class="sxs-lookup"><span data-stu-id="82557-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="82557-153">默认情况下，`SaveTokens` 设置为 `false` 以减小最终身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="82557-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="82557-154">示例应用在 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>中将 `SaveTokens` 的值设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="82557-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="82557-155">`OnPostConfirmationAsync` 执行时，从 `ApplicationUser`的 `AuthenticationProperties`中的外部提供程序存储访问令牌（[AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)）。</span><span class="sxs-lookup"><span data-stu-id="82557-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="82557-156">示例应用将访问令牌保存在*帐户/ExternalLogin*的 `OnPostConfirmationAsync` （新用户注册）和 `OnGetCallbackAsync` （以前注册的用户）中：</span><span class="sxs-lookup"><span data-stu-id="82557-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="82557-157">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="82557-157">How to add additional custom tokens</span></span>

<span data-ttu-id="82557-158">为了演示如何添加作为 `SaveTokens`的一部分存储的自定义令牌，示例应用添加了一个 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>，其中包含当前 <xref:System.DateTime> 的[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)为 `TicketCreated`：</span><span class="sxs-lookup"><span data-stu-id="82557-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="82557-159">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="82557-159">Creating and adding claims</span></span>

<span data-ttu-id="82557-160">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="82557-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="82557-161">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="82557-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="82557-162">用户可以通过从 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 派生并实现抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 方法来定义自定义操作。</span><span class="sxs-lookup"><span data-stu-id="82557-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="82557-163">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="82557-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="82557-164">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="82557-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="82557-165">[ClaimActionCollection （String）](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)从集合中移除给定 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 的所有声明操作。</span><span class="sxs-lookup"><span data-stu-id="82557-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="82557-166">[ClaimActionCollectionMapExtensions. DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)从标识中删除给定 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 的声明。</span><span class="sxs-lookup"><span data-stu-id="82557-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="82557-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要与[OpenID connect （OIDC）](/azure/active-directory/develop/v2-protocols-oidc)一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="82557-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="82557-168">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="82557-168">Sample app output</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="82557-169">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="82557-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="82557-170">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="82557-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="82557-171">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82557-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82557-172">必备条件</span><span class="sxs-lookup"><span data-stu-id="82557-172">Prerequisites</span></span>

<span data-ttu-id="82557-173">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="82557-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="82557-174">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="82557-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="82557-175">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="82557-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="82557-176">示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="82557-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="82557-177">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="82557-177">Set the client ID and client secret</span></span>

<span data-ttu-id="82557-178">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="82557-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="82557-179">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="82557-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="82557-180">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="82557-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="82557-181">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="82557-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="82557-182">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="82557-183">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="82557-184">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="82557-185">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="82557-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="82557-186">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="82557-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="82557-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="82557-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="82557-188">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="82557-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="82557-189">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="82557-189">Establish the authentication scope</span></span>

<span data-ttu-id="82557-190">通过指定 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>指定要从提供程序检索的权限的列表。</span><span class="sxs-lookup"><span data-stu-id="82557-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="82557-191">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="82557-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="82557-192">提供程序</span><span class="sxs-lookup"><span data-stu-id="82557-192">Provider</span></span>  | <span data-ttu-id="82557-193">范围</span><span class="sxs-lookup"><span data-stu-id="82557-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="82557-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="82557-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="82557-195">Google</span><span class="sxs-lookup"><span data-stu-id="82557-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="82557-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="82557-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="82557-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="82557-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="82557-198">在示例应用中，当对 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>调用 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 时，框架会自动添加 Google 的 `userinfo.profile` 范围。</span><span class="sxs-lookup"><span data-stu-id="82557-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="82557-199">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="82557-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="82557-200">在下面的示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以便检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="82557-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="82557-201">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="82557-201">Map user data keys and create claims</span></span>

<span data-ttu-id="82557-202">在提供程序的选项中，为外部提供程序的 JSON 用户数据中的每个键/子项指定 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 或 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*>，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="82557-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="82557-203">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes>。</span><span class="sxs-lookup"><span data-stu-id="82557-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="82557-204">该示例应用在 Google 用户数据中的 `locale` 和 `picture` 密钥中创建区域设置（`urn:google:locale`）和图片（`urn:google:picture`）声明：</span><span class="sxs-lookup"><span data-stu-id="82557-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="82557-205">在 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`中，<xref:Microsoft.AspNetCore.Identity.IdentityUser> （`ApplicationUser`）使用 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>登录到应用中。</span><span class="sxs-lookup"><span data-stu-id="82557-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="82557-206">在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以存储 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>提供的用户数据的 `ApplicationUser` 声明。</span><span class="sxs-lookup"><span data-stu-id="82557-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="82557-207">在示例应用中，`OnPostConfirmationAsync` （*Account/ExternalLogin*）为已登录的 `ApplicationUser`建立了区域设置（`urn:google:locale`）和图片（`urn:google:picture`）声明，其中包括 <xref:System.Security.Claims.ClaimTypes.GivenName>的声明：</span><span class="sxs-lookup"><span data-stu-id="82557-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="82557-208">默认情况下，用户的声明存储在身份验证 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="82557-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="82557-209">如果身份验证 cookie 太大，则可能会导致应用程序失败，因为：</span><span class="sxs-lookup"><span data-stu-id="82557-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="82557-210">浏览器检测到 cookie 标头太长。</span><span class="sxs-lookup"><span data-stu-id="82557-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="82557-211">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="82557-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="82557-212">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="82557-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="82557-213">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="82557-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="82557-214">使用 Cookie 身份验证中间件 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 的自定义 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="82557-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="82557-215">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="82557-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="82557-216">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="82557-216">Save the access token</span></span>

<span data-ttu-id="82557-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义在授权成功后是否应将访问令牌和刷新令牌存储到 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 中。</span><span class="sxs-lookup"><span data-stu-id="82557-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="82557-218">默认情况下，`SaveTokens` 设置为 `false` 以减小最终身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="82557-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="82557-219">示例应用在 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>中将 `SaveTokens` 的值设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="82557-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="82557-220">`OnPostConfirmationAsync` 执行时，从 `ApplicationUser`的 `AuthenticationProperties`中的外部提供程序存储访问令牌（[AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)）。</span><span class="sxs-lookup"><span data-stu-id="82557-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="82557-221">示例应用将访问令牌保存在*帐户/ExternalLogin*的 `OnPostConfirmationAsync` （新用户注册）和 `OnGetCallbackAsync` （以前注册的用户）中：</span><span class="sxs-lookup"><span data-stu-id="82557-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="82557-222">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="82557-222">How to add additional custom tokens</span></span>

<span data-ttu-id="82557-223">为了演示如何添加作为 `SaveTokens`的一部分存储的自定义令牌，示例应用添加了一个 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>，其中包含当前 <xref:System.DateTime> 的[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)为 `TicketCreated`：</span><span class="sxs-lookup"><span data-stu-id="82557-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="82557-224">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="82557-224">Creating and adding claims</span></span>

<span data-ttu-id="82557-225">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="82557-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="82557-226">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="82557-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="82557-227">用户可以通过从 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 派生并实现抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 方法来定义自定义操作。</span><span class="sxs-lookup"><span data-stu-id="82557-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="82557-228">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="82557-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="82557-229">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="82557-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="82557-230">[ClaimActionCollection （String）](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)从集合中移除给定 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 的所有声明操作。</span><span class="sxs-lookup"><span data-stu-id="82557-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="82557-231">[ClaimActionCollectionMapExtensions. DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)从标识中删除给定 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 的声明。</span><span class="sxs-lookup"><span data-stu-id="82557-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="82557-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要与[OpenID connect （OIDC）](/azure/active-directory/develop/v2-protocols-oidc)一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="82557-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="82557-233">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="82557-233">Sample app output</span></span>

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

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="82557-234">其他资源</span><span class="sxs-lookup"><span data-stu-id="82557-234">Additional resources</span></span>

* <span data-ttu-id="82557-235">[dotnet/AspNetCore 工程 SocialSample 应用](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)&ndash; 链接的示例应用位于[Dotnet/AspNetCore GitHub](https://github.com/dotnet/AspNetCore)存储库的 `master` 工程分支。</span><span class="sxs-lookup"><span data-stu-id="82557-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="82557-236">对于 ASP.NET Core 的下一版本，`master` 分支包含处于活动开发下的代码。</span><span class="sxs-lookup"><span data-stu-id="82557-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="82557-237">若要查看 ASP.NET Core 的已发布版本的示例应用的版本，请使用**分支**下拉列表选择发布分支（例如 `release/{X.Y}`）。</span><span class="sxs-lookup"><span data-stu-id="82557-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
