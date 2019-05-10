---
title: 保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌
author: guardrex
description: 了解如何建立其他声明和来自外部提供程序的令牌。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 37c7a51217576669bcaed79d4a212e6412aa8945
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897664"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="fbad3-103">保留其他声明和 ASP.NET Core 中的外部提供程序颁发令牌</span><span class="sxs-lookup"><span data-stu-id="fbad3-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="fbad3-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fbad3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fbad3-105">ASP.NET Core 应用可以建立其他声明和来自外部身份验证提供程序，如 Facebook、 Google、 Microsoft 和 Twitter 令牌。</span><span class="sxs-lookup"><span data-stu-id="fbad3-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="fbad3-106">每个提供程序会显示不同用户的信息在其平台上，但接收并将用户数据转换成其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="fbad3-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="fbad3-107">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fbad3-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fbad3-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="fbad3-108">Prerequisites</span></span>

<span data-ttu-id="fbad3-109">决定哪些外部身份验证提供程序支持应用程序中。</span><span class="sxs-lookup"><span data-stu-id="fbad3-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="fbad3-110">对于每个提供程序，注册应用程序和获取客户端 ID 和客户端机密。</span><span class="sxs-lookup"><span data-stu-id="fbad3-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="fbad3-111">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="fbad3-112">[示例应用](#sample-app-instructions)使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="fbad3-112">The [sample app](#sample-app-instructions) uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="fbad3-113">设置客户端 ID 和客户端机密</span><span class="sxs-lookup"><span data-stu-id="fbad3-113">Set the client ID and client secret</span></span>

<span data-ttu-id="fbad3-114">OAuth 身份验证提供程序使用客户端 ID 和客户端机密的应用与建立信任关系。</span><span class="sxs-lookup"><span data-stu-id="fbad3-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="fbad3-115">客户端 ID 和客户端密钥值的应用的外部身份验证提供程序时创建应用注册到提供程序。</span><span class="sxs-lookup"><span data-stu-id="fbad3-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="fbad3-116">提供程序的客户端 ID 和客户端机密，必须单独配置每个应用程序使用的外部提供程序。</span><span class="sxs-lookup"><span data-stu-id="fbad3-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="fbad3-117">有关详细信息，请参阅适用于方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="fbad3-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="fbad3-118">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="fbad3-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="fbad3-119">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="fbad3-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="fbad3-120">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="fbad3-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="fbad3-121">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="fbad3-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="fbad3-122">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="fbad3-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="fbad3-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="fbad3-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="fbad3-124">示例应用程序使用客户端 ID 和 Google 提供的客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="fbad3-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="fbad3-125">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="fbad3-125">Establish the authentication scope</span></span>

<span data-ttu-id="fbad3-126">指定要从提供程序检索由指定的权限列表<xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="fbad3-127">下表中显示常见的外部提供程序的身份验证作用域。</span><span class="sxs-lookup"><span data-stu-id="fbad3-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="fbad3-128">提供程序</span><span class="sxs-lookup"><span data-stu-id="fbad3-128">Provider</span></span>  | <span data-ttu-id="fbad3-129">范围</span><span class="sxs-lookup"><span data-stu-id="fbad3-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="fbad3-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="fbad3-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="fbad3-131">Google</span><span class="sxs-lookup"><span data-stu-id="fbad3-131">Google</span></span>    | `https://www.googleapis.com/auth/plus.login`                     |
| <span data-ttu-id="fbad3-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="fbad3-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="fbad3-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="fbad3-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="fbad3-134">该示例应用将添加 Google`plus.login`作用域以请求登录 Google + 中的权限：</span><span class="sxs-lookup"><span data-stu-id="fbad3-134">The sample app adds the Google `plus.login` scope to request Google+ sign in permissions:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="fbad3-135">将用户数据键映射和创建声明</span><span class="sxs-lookup"><span data-stu-id="fbad3-135">Map user data keys and create claims</span></span>

<span data-ttu-id="fbad3-136">在提供程序的选项中，指定<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*>要阅读在登录的应用程序标识的外部提供程序的 JSON 用户数据中的每个键。</span><span class="sxs-lookup"><span data-stu-id="fbad3-136">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> for each key in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="fbad3-137">声明类型的详细信息，请参阅<xref:System.Security.Claims.ClaimTypes>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-137">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="fbad3-138">示例应用创建<xref:System.Security.Claims.ClaimTypes.Gender>将来自声明`gender`Google 用户数据中的键：</span><span class="sxs-lookup"><span data-stu-id="fbad3-138">The sample app creates a <xref:System.Security.Claims.ClaimTypes.Gender> claim from the `gender` key in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

<span data-ttu-id="fbad3-139">在中<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>、 一个<xref:Microsoft.AspNetCore.Identity.IdentityUser>(`ApplicationUser`) 登录到应用程序与<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-139">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="fbad3-140">在登录过程中，<xref:Microsoft.AspNetCore.Identity.UserManager%601>可以存储`ApplicationUser`声明的用户数据可从<xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-140">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claim for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="fbad3-141">在示例应用`OnPostConfirmationAsync`(*Account/ExternalLogin.cshtml.cs*) 建立<xref:System.Security.Claims.ClaimTypes.Gender>声明的签名中`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="fbad3-141">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes a <xref:System.Security.Claims.ClaimTypes.Gender> claim for the signed in `ApplicationUser`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a><span data-ttu-id="fbad3-142">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="fbad3-142">Save the access token</span></span>

<span data-ttu-id="fbad3-143"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定义访问和刷新令牌是否应存储在<xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties>成功授权后。</span><span class="sxs-lookup"><span data-stu-id="fbad3-143"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="fbad3-144">`SaveTokens` 设置为`false`默认情况下以减小最终的身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="fbad3-144">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="fbad3-145">示例应用程序设置的值`SaveTokens`到`true`中<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="fbad3-145">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

<span data-ttu-id="fbad3-146">当`OnPostConfirmationAsync`执行时，存储的访问令牌 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) 中的外部提供程序从`ApplicationUser`的`AuthenticationProperties`。</span><span class="sxs-lookup"><span data-stu-id="fbad3-146">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="fbad3-147">示例应用将保存中的访问令牌：</span><span class="sxs-lookup"><span data-stu-id="fbad3-147">The sample app saves the access token in:</span></span>

* <span data-ttu-id="fbad3-148">`OnPostConfirmationAsync` &ndash; 执行新的用户注册。</span><span class="sxs-lookup"><span data-stu-id="fbad3-148">`OnPostConfirmationAsync` &ndash; Executes for new user registration.</span></span>
* <span data-ttu-id="fbad3-149">`OnGetCallbackAsync` &ndash; 在以前已注册的用户登录到应用时执行。</span><span class="sxs-lookup"><span data-stu-id="fbad3-149">`OnGetCallbackAsync` &ndash; Executes when a previously registered user signs into the app.</span></span>

<span data-ttu-id="fbad3-150">*Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fbad3-150">*Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="fbad3-151">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="fbad3-151">How to add additional custom tokens</span></span>

<span data-ttu-id="fbad3-152">若要演示如何添加作为的一部分存储的自定义令牌`SaveTokens`，示例应用添加<xref:Microsoft.AspNetCore.Authentication.AuthenticationToken>与当前<xref:System.DateTime>有关[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)的`TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="fbad3-152">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a><span data-ttu-id="fbad3-153">示例应用程序说明</span><span class="sxs-lookup"><span data-stu-id="fbad3-153">Sample app instructions</span></span>

<span data-ttu-id="fbad3-154">示例应用演示了如何：</span><span class="sxs-lookup"><span data-stu-id="fbad3-154">The sample app demonstrates how to:</span></span>

* <span data-ttu-id="fbad3-155">从 Google 获取用户的性别和存储的值的性别声明。</span><span class="sxs-lookup"><span data-stu-id="fbad3-155">Obtain the user's gender from Google and store a gender claim with the value.</span></span>
* <span data-ttu-id="fbad3-156">将 Google 访问令牌存储在用户的`AuthenticationProperties`。</span><span class="sxs-lookup"><span data-stu-id="fbad3-156">Store the Google access token in the user's `AuthenticationProperties`.</span></span>

<span data-ttu-id="fbad3-157">若要使用示例应用程序：</span><span class="sxs-lookup"><span data-stu-id="fbad3-157">To use the sample app:</span></span>

1. <span data-ttu-id="fbad3-158">注册应用程序和获取有效的客户端 ID 和 Google 身份验证的客户端机密。</span><span class="sxs-lookup"><span data-stu-id="fbad3-158">Register the app and obtain a valid client ID and client secret for Google authentication.</span></span> <span data-ttu-id="fbad3-159">有关详细信息，请参阅 <xref:security/authentication/google-logins>。</span><span class="sxs-lookup"><span data-stu-id="fbad3-159">For more information, see <xref:security/authentication/google-logins>.</span></span>
1. <span data-ttu-id="fbad3-160">提供的客户端 ID 和客户端密钥对中的应用程序<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>的`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="fbad3-160">Provide the client ID and client secret to the app in the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> of `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="fbad3-161">运行应用并请求我声明页。</span><span class="sxs-lookup"><span data-stu-id="fbad3-161">Run the app and request the My Claims page.</span></span> <span data-ttu-id="fbad3-162">如果用户未登录，该应用将重定向到 Google。</span><span class="sxs-lookup"><span data-stu-id="fbad3-162">When the user isn't signed in, the app redirects to Google.</span></span> <span data-ttu-id="fbad3-163">使用 Google 登录。</span><span class="sxs-lookup"><span data-stu-id="fbad3-163">Sign in with Google.</span></span> <span data-ttu-id="fbad3-164">Google 将用户重定向回应用程序 (`/Home/MyClaims`)。</span><span class="sxs-lookup"><span data-stu-id="fbad3-164">Google redirects the user back to the app (`/Home/MyClaims`).</span></span> <span data-ttu-id="fbad3-165">用户进行身份验证，并加载我的声明页。</span><span class="sxs-lookup"><span data-stu-id="fbad3-165">The user is authenticated, and the My Claims page is loaded.</span></span> <span data-ttu-id="fbad3-166">不存在下性别声明**用户声明**使用从 Google 获取的值。</span><span class="sxs-lookup"><span data-stu-id="fbad3-166">The gender claim is present under **User Claims** with the value obtained from Google.</span></span> <span data-ttu-id="fbad3-167">访问令牌显示在**身份验证属性**。</span><span class="sxs-lookup"><span data-stu-id="fbad3-167">The access token appears in the **Authentication Properties**.</span></span>

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
