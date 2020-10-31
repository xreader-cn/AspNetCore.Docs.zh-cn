---
title: 在 ASP.NET Core 中保存外部提供程序的其他声明和令牌
author: rick-anderson
description: 了解如何建立来自外部提供程序的其他声明和标记。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141503"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="6ee20-103">在 ASP.NET Core 中保存外部提供程序的其他声明和令牌</span><span class="sxs-lookup"><span data-stu-id="6ee20-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ee20-104">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="6ee20-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6ee20-105">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="6ee20-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6ee20-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6ee20-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ee20-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="6ee20-107">Prerequisites</span></span>

<span data-ttu-id="6ee20-108">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="6ee20-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6ee20-109">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="6ee20-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6ee20-110">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6ee20-111">示例应用使用 [Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="6ee20-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6ee20-112">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="6ee20-112">Set the client ID and client secret</span></span>

<span data-ttu-id="6ee20-113">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="6ee20-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6ee20-114">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="6ee20-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6ee20-115">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="6ee20-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6ee20-116">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="6ee20-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6ee20-117">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6ee20-118">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6ee20-119">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6ee20-120">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6ee20-121">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="6ee20-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6ee20-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6ee20-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6ee20-123">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="6ee20-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6ee20-124">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="6ee20-124">Establish the authentication scope</span></span>

<span data-ttu-id="6ee20-125">指定要从提供程序检索的权限的列表 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6ee20-126">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="6ee20-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6ee20-127">提供程序</span><span class="sxs-lookup"><span data-stu-id="6ee20-127">Provider</span></span>  | <span data-ttu-id="6ee20-128">范围</span><span class="sxs-lookup"><span data-stu-id="6ee20-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="6ee20-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="6ee20-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="6ee20-130">Google</span><span class="sxs-lookup"><span data-stu-id="6ee20-130">Google</span></span>    | <span data-ttu-id="6ee20-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="6ee20-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="6ee20-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="6ee20-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="6ee20-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="6ee20-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="6ee20-134">在示例应用中， `profile` `email` `openid` 当在上调用时，由框架自动添加 Google 的、和作用域 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6ee20-135">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="6ee20-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6ee20-136">在以下示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="6ee20-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6ee20-137">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-137">Map user data keys and create claims</span></span>

<span data-ttu-id="6ee20-138">在提供程序的选项中，为 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 外部提供程序的 JSON 用户数据中的每个键/子项指定或，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="6ee20-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6ee20-139">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6ee20-140">该示例应用程序 (`urn:google:locale`) 和图片 (`urn:google:picture` 从 `locale` `picture` Google 用户数据中的和密钥) 声明创建区域设置：</span><span class="sxs-lookup"><span data-stu-id="6ee20-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6ee20-141">在中 `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) 使用登录到应用 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-141">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6ee20-142">在登录过程中， <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> 可以存储 `ApplicationUser` 提供的用户数据的声明 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-142">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6ee20-143">在示例应用中， `OnPostConfirmationAsync` ( *帐户/ExternalLogin* ) 将 (的 `urn:google:locale`) 和图片 (`urn:google:picture`) 声明中建立区域设置 `ApplicationUser` ，包括的声明 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-143">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6ee20-144">默认情况下，会在身份验证中存储用户的声明 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-144">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="6ee20-145">如果身份验证 :::no-loc(cookie)::: 太大，则可能会导致应用失败，原因是：</span><span class="sxs-lookup"><span data-stu-id="6ee20-145">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6ee20-146">浏览器检测到 :::no-loc(cookie)::: 标头太长。</span><span class="sxs-lookup"><span data-stu-id="6ee20-146">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="6ee20-147">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="6ee20-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="6ee20-148">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="6ee20-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6ee20-149">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="6ee20-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6ee20-150">使用 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> 用于 :::no-loc(Cookie)::: 身份验证中间件的自定义 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="6ee20-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6ee20-151">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="6ee20-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6ee20-152">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="6ee20-152">Save the access token</span></span>

<span data-ttu-id="6ee20-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义在授权成功后是否应将访问和刷新令牌存储在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6ee20-154">`SaveTokens` 默认情况下，设置为以 `false` 减小最终身份验证的大小 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="6ee20-155">示例应用将的值设置 `SaveTokens` 为 `true` 中的 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6ee20-156">`OnPostConfirmationAsync`执行时，将访问令牌存储在的外部提供程序中， ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) 。 `ApplicationUser` `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="6ee20-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6ee20-157">示例应用程序将访问令牌保存在 `OnPostConfirmationAsync` (新用户注册) 并 `OnGetCallbackAsync` (之前注册的用户) *帐户/ExternalLogin 中。*</span><span class="sxs-lookup"><span data-stu-id="6ee20-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6ee20-158">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="6ee20-158">How to add additional custom tokens</span></span>

<span data-ttu-id="6ee20-159">为了演示如何添加作为的一部分存储的自定义令牌， `SaveTokens` 示例应用程序会 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> 为的 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 添加一个，其中包含 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6ee20-160">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-160">Creating and adding claims</span></span>

<span data-ttu-id="6ee20-161">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6ee20-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6ee20-162">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6ee20-163">用户可以通过从派生 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 并实现抽象方法来定义自定义操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6ee20-164">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6ee20-165">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="6ee20-166">[ClaimActionCollection (字符串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 将从集合中移除给定的所有声明操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6ee20-167">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 从标识中删除给定的声明 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6ee20-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要与 [OpenID connect (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="6ee20-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6ee20-169">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="6ee20-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

<span data-ttu-id="6ee20-170">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="6ee20-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6ee20-171">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="6ee20-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6ee20-172">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6ee20-172">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ee20-173">先决条件</span><span class="sxs-lookup"><span data-stu-id="6ee20-173">Prerequisites</span></span>

<span data-ttu-id="6ee20-174">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="6ee20-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6ee20-175">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="6ee20-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6ee20-176">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6ee20-177">示例应用使用 [Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="6ee20-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6ee20-178">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="6ee20-178">Set the client ID and client secret</span></span>

<span data-ttu-id="6ee20-179">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="6ee20-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6ee20-180">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="6ee20-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6ee20-181">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="6ee20-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6ee20-182">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="6ee20-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6ee20-183">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6ee20-184">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6ee20-185">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6ee20-186">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="6ee20-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6ee20-187">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="6ee20-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6ee20-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6ee20-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6ee20-189">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="6ee20-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6ee20-190">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="6ee20-190">Establish the authentication scope</span></span>

<span data-ttu-id="6ee20-191">指定要从提供程序检索的权限的列表 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6ee20-192">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="6ee20-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6ee20-193">提供程序</span><span class="sxs-lookup"><span data-stu-id="6ee20-193">Provider</span></span>  | <span data-ttu-id="6ee20-194">范围</span><span class="sxs-lookup"><span data-stu-id="6ee20-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="6ee20-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="6ee20-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="6ee20-196">Google</span><span class="sxs-lookup"><span data-stu-id="6ee20-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="6ee20-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="6ee20-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="6ee20-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="6ee20-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="6ee20-199">在示例应用中， `userinfo.profile` 当在上调用时，由框架自动添加 Google 的作用域 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6ee20-200">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="6ee20-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6ee20-201">在以下示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以便检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="6ee20-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6ee20-202">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-202">Map user data keys and create claims</span></span>

<span data-ttu-id="6ee20-203">在提供程序的选项中，为 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 外部提供程序的 JSON 用户数据中的每个键/子项指定或，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="6ee20-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6ee20-204">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6ee20-205">该示例应用程序 (`urn:google:locale`) 和图片 (`urn:google:picture` 从 `locale` `picture` Google 用户数据中的和密钥) 声明创建区域设置：</span><span class="sxs-lookup"><span data-stu-id="6ee20-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6ee20-206">在中 `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) 使用登录到应用 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-206">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6ee20-207">在登录过程中， <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> 可以存储 `ApplicationUser` 提供的用户数据的声明 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-207">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6ee20-208">在示例应用中， `OnPostConfirmationAsync` ( *帐户/ExternalLogin* ) 将 (的 `urn:google:locale`) 和图片 (`urn:google:picture`) 声明中建立区域设置 `ApplicationUser` ，包括的声明 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-208">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6ee20-209">默认情况下，会在身份验证中存储用户的声明 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-209">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="6ee20-210">如果身份验证 :::no-loc(cookie)::: 太大，则可能会导致应用失败，原因是：</span><span class="sxs-lookup"><span data-stu-id="6ee20-210">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6ee20-211">浏览器检测到 :::no-loc(cookie)::: 标头太长。</span><span class="sxs-lookup"><span data-stu-id="6ee20-211">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="6ee20-212">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="6ee20-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="6ee20-213">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="6ee20-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6ee20-214">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="6ee20-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6ee20-215">使用 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> 用于 :::no-loc(Cookie)::: 身份验证中间件的自定义 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="6ee20-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6ee20-216">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="6ee20-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6ee20-217">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="6ee20-217">Save the access token</span></span>

<span data-ttu-id="6ee20-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义在授权成功后是否应将访问和刷新令牌存储在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6ee20-219">`SaveTokens` 默认情况下，设置为以 `false` 减小最终身份验证的大小 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="6ee20-220">示例应用将的值设置 `SaveTokens` 为 `true` 中的 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6ee20-221">`OnPostConfirmationAsync`执行时，将访问令牌存储在的外部提供程序中， ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) 。 `ApplicationUser` `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="6ee20-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6ee20-222">示例应用程序将访问令牌保存在 `OnPostConfirmationAsync` (新用户注册) 并 `OnGetCallbackAsync` (之前注册的用户) *帐户/ExternalLogin 中。*</span><span class="sxs-lookup"><span data-stu-id="6ee20-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6ee20-223">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="6ee20-223">How to add additional custom tokens</span></span>

<span data-ttu-id="6ee20-224">为了演示如何添加作为的一部分存储的自定义令牌， `SaveTokens` 示例应用程序会 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> 为的 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 添加一个，其中包含 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="6ee20-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6ee20-225">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-225">Creating and adding claims</span></span>

<span data-ttu-id="6ee20-226">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6ee20-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6ee20-227">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6ee20-228">用户可以通过从派生 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 并实现抽象方法来定义自定义操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6ee20-229">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="6ee20-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6ee20-230">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="6ee20-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="6ee20-231">[ClaimActionCollection (字符串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 将从集合中移除给定的所有声明操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6ee20-232">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 从标识中删除给定的声明 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="6ee20-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6ee20-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要与 [OpenID connect (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="6ee20-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6ee20-234">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="6ee20-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

## <a name="additional-resources"></a><span data-ttu-id="6ee20-235">其他资源</span><span class="sxs-lookup"><span data-stu-id="6ee20-235">Additional resources</span></span>

* <span data-ttu-id="6ee20-236">[dotnet/AspNetCore 工程 SocialSample 应用](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)：链接的示例应用位于 [Dotnet/AspNetCore GitHub 存储库的](https://github.com/dotnet/AspNetCore) `master` 工程分支中。</span><span class="sxs-lookup"><span data-stu-id="6ee20-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="6ee20-237">`master`分支包含处于活动开发下的下一版本 ASP.NET Core 的代码。</span><span class="sxs-lookup"><span data-stu-id="6ee20-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="6ee20-238">若要查看 ASP.NET Core 的已发布版本的示例应用的版本，请使用 **分支** 下拉列表选择发布分支 (例如 `release/{X.Y}`) "。</span><span class="sxs-lookup"><span data-stu-id="6ee20-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
