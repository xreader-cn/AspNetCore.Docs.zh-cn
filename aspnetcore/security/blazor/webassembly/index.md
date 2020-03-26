---
title: 保护 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何将 Blazor WebAssemlby 应用作为单页应用程序 (SPA) 进行保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 652d4c61110f786396d9d5af4f131b817c40e333
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219241"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="6bc8b-103">保护 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6bc8b-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6bc8b-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="6bc8b-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="6bc8b-105"> WebAssembly 应用与单页应用程序 (SPA) 的保护方式相同。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="6bc8b-106">可通过多种方式向 SPA 进行用户身份验证，但最常用、最全面的方式是使用基于 [oAuth 2.0 协议](https://oauth.net/)的实现，例如 [Open ID Connect (OIDC)](https://openid.net/connect/)。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [oAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="6bc8b-107">身份验证库</span><span class="sxs-lookup"><span data-stu-id="6bc8b-107">Authentication library</span></span>

Blazor<span data-ttu-id="6bc8b-108"> WebAssembly 支持通过 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库使用 OIDC 对应用进行身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="6bc8b-109">该库提供一组基元，可用于对 ASP.NET Core 后端进行无缝身份验证。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="6bc8b-110">该库将 ASP.NET Core 标志与以[标识服务器](https://identityserver.io/)为基础的 API 身份验证集成。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="6bc8b-111">该库可以针对支持 OIDC 的任何第三方标识提供者 (IP)，即 OpenID 提供者 (OP)，进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="6bc8b-112">Blazor WebAssembly 中的身份验证支持建立在 *oidc-client.js* 库的基础之上，该库用于处理底层身份验证协议详细信息。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="6bc8b-113">还有对 SPA 进行身份验证的其他选项，例如使用 SameSite cookie。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="6bc8b-114">但是，Blazor WebAssembly 的工程设计决定，oAuth 和 OIDC 是在 Blazor WebAssembly 应用中进行身份验证的最佳选择。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-114">However, the engineering design of Blazor WebAssembly is settled on oAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="6bc8b-115">出于以下功能和安全原因，选择了以 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 为基础的[基于令牌的身份验证](xref:security/anti-request-forgery#token-based-authentication)而不是[基于 cookie 的身份验证](xref:security/anti-request-forgery#cookie-based-authentication)：</span><span class="sxs-lookup"><span data-stu-id="6bc8b-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="6bc8b-116">使用基于令牌的协议可以减小攻击面，因为并非所有请求中都会发送令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="6bc8b-117">服务器终结点不要求针对[跨站点请求伪造 (CSRF)](xref:security/anti-request-forgery) 进行保护，因为会显式发送令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="6bc8b-118">因此，可以将 Blazor WebAssembly 应用与 MVC 或 Razor Pages 应用托管在同一位置。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="6bc8b-119">令牌的权限比 cookie 窄。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="6bc8b-120">例如，令牌不能用于管理用户帐户或更改用户密码，除非显式实现了此类功能。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="6bc8b-121">令牌的生命周期更短（默认为一小时），这限制了攻击时间窗口。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="6bc8b-122">还可随时撤销令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="6bc8b-123">自包含 JWT 向客户端和服务器提供身份验证进程保证。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="6bc8b-124">例如，客户端可以检测和验证它收到的令牌是否合法，以及是否是在给定身份验证过程中发出的。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="6bc8b-125">如果有第三方尝试在身份验证进程中偷换令牌，客户端可以检测被偷换的令牌并避免使用它。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="6bc8b-126">oAuth 和 OIDC 的令牌不依赖于用户代理行为正确以确保应用安全。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-126">Tokens with oAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="6bc8b-127">基于令牌的协议（例如 oAuth 和 OIDC）允许用同一组安全特征对托管和独立应用进行验证和授权。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-127">Token-based protocols, such as oAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="6bc8b-128">使用 OIDC 的身份验证进程</span><span class="sxs-lookup"><span data-stu-id="6bc8b-128">Authentication process with OIDC</span></span>

<span data-ttu-id="6bc8b-129">`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库提供几个基元，用于实现使用 OIDC 的身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="6bc8b-130">从广义上说来，身份验证的原理如下：</span><span class="sxs-lookup"><span data-stu-id="6bc8b-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="6bc8b-131">当匿名用户选择登录按钮或请求应用了 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性的页面时，会将其重定向到应用的登录页 (`/authentication/login`)。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="6bc8b-132">在登录页上，身份验证库准备重定向到授权终结点。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="6bc8b-133">授权终结点在 Blazor WebAssembly 应用外部，可托管在独立的源。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="6bc8b-134">该终结点负责确定用户是否通过身份验证，并发送一个或更多令牌作为响应。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="6bc8b-135">身份验证库提供登录回叫以接收身份验证响应。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="6bc8b-136">如果用户未通过身份验证，会将其重定向到底层身份验证系统，通常是 ASP.NET Core 标识。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="6bc8b-137">如果用户已通过身份验证，则授权终结点会生成相应的令牌，并将浏览器重定向回登录回叫终结点 (`/authentication/login-callback`)。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="6bc8b-138">当 Blazor WebAssembly 应用加载登录回叫终结点 (`/authentication/login-callback`) 时，就处理了身份验证进程。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="6bc8b-139">如果身份验证进程成功完成，则用户通过身份验证，可以选择返回该用户请求的原受保护 URL。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="6bc8b-140">如果身份验证进程由于任何原因而失败，会将用户导向登录失败页 (`/authentication/login-failed`)，并显示错误。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="6bc8b-141">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="6bc8b-141">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="6bc8b-142">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-142">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="6bc8b-143">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-143">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="6bc8b-144">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-144">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="6bc8b-145">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="6bc8b-145">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="6bc8b-146">使用针对第三方 API 提供程序的客户端 oAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="6bc8b-146">Authenticate the user with a client-side oAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="6bc8b-147">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="6bc8b-147">In this scenario:</span></span>

* <span data-ttu-id="6bc8b-148">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-148">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="6bc8b-149">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-149">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="6bc8b-150">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-150">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="6bc8b-151">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="6bc8b-151">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="6bc8b-152">使用第三方登录提供程序配置标识。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-152">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="6bc8b-153">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-153">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="6bc8b-154">当用户登录时，标识将在身份验证过程中收集访问和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-154">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="6bc8b-155">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-155">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="6bc8b-156">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="6bc8b-156">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="6bc8b-157">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-157">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="6bc8b-158">在此处，使用第三方访问令牌直接从客户端上的标识调用第三方 API 资源。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-158">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="6bc8b-159">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-159">We don't recommend this approach.</span></span> <span data-ttu-id="6bc8b-160">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-160">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="6bc8b-161">在 oAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用来安全地存储机密，并且访问令牌是为机密客户端而生成的。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-161">In oAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="6bc8b-162">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-162">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="6bc8b-163">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-163">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="6bc8b-164">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-164">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="6bc8b-165">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="6bc8b-165">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="6bc8b-166">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-166">Make an API call from the client to the server API.</span></span> <span data-ttu-id="6bc8b-167">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-167">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="6bc8b-168">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="6bc8b-168">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="6bc8b-169">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-169">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="6bc8b-170">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6bc8b-170">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
