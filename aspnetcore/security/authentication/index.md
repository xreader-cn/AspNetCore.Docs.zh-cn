---
title: ASP.NET Core 身份验证概述
author: mjrousos
description: 了解 ASP.NET Core 中的身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 1/24/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/index
ms.openlocfilehash: 72036e9c4c92ee5dd82ac4a67e766fb0e5c8f924
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057286"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="4584c-103">ASP.NET Core 身份验证概述</span><span class="sxs-lookup"><span data-stu-id="4584c-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="4584c-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="4584c-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="4584c-105">身份验证是确定用户身份的过程。</span><span class="sxs-lookup"><span data-stu-id="4584c-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="4584c-106">[授权](xref:security/authorization/introduction)是确定用户是否有权访问资源的过程。</span><span class="sxs-lookup"><span data-stu-id="4584c-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="4584c-107">在 ASP.NET Core 中，身份验证由 `IAuthenticationService` 负责，而它供身份验证[中间件](xref:fundamentals/middleware/index)使用。</span><span class="sxs-lookup"><span data-stu-id="4584c-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="4584c-108">身份验证服务会使用已注册的身份验证处理程序来完成与身份验证相关的操作。</span><span class="sxs-lookup"><span data-stu-id="4584c-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="4584c-109">与身份验证相关的操作示例包括：</span><span class="sxs-lookup"><span data-stu-id="4584c-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="4584c-110">对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4584c-110">Authenticating a user.</span></span>
* <span data-ttu-id="4584c-111">在未经身份验证的用户试图访问受限资源时作出响应。</span><span class="sxs-lookup"><span data-stu-id="4584c-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="4584c-112">已注册的身份验证处理程序及其配置选项被称为“方案”。</span><span class="sxs-lookup"><span data-stu-id="4584c-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="4584c-113">身份验证方案由 `Startup.ConfigureServices` 中的注册身份验证服务指定：</span><span class="sxs-lookup"><span data-stu-id="4584c-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="4584c-114">方式是在调用 `services.AddAuthentication` 后调用方案特定的扩展方法（例如 `AddJwtBearer` 或 `AddCookie`）。</span><span class="sxs-lookup"><span data-stu-id="4584c-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="4584c-115">这些扩展方法使用 [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) 向适当的设置注册方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="4584c-116">比较不常用的方式是直接调用 [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*)。</span><span class="sxs-lookup"><span data-stu-id="4584c-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="4584c-117">例如，下列代码会为 cookie 和 JWT 持有者身份验证方案注册身份验证服务和处理程序：</span><span class="sxs-lookup"><span data-stu-id="4584c-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="4584c-118">`AddAuthentication` 参数 `JwtBearerDefaults.AuthenticationScheme` 是方案的名称，未请求特定方案时会默认使用此名称。</span><span class="sxs-lookup"><span data-stu-id="4584c-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="4584c-119">如果使用了多个方案，授权策略（或授权属性）可[指定](xref:security/authorization/limitingidentitybyscheme)对用户进行身份验证时要依据的一个或多个身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="4584c-120">在上例中，可通过指定 cookie 身份验证方案的名称来使用该方案（默认为 `CookieAuthenticationDefaults.AuthenticationScheme`，但也可在调用 `AddCookie` 时提供其他名称）。</span><span class="sxs-lookup"><span data-stu-id="4584c-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="4584c-121">在某些情况下，其他扩展方法会自动调用 `AddAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="4584c-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="4584c-122">例如，在使用 [ASP.NET Core Identity](xref:security/authentication/identity) 时，会在内部调用 `AddAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="4584c-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="4584c-123">通过在应用的 `IApplicationBuilder` 上调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 扩展方法，在 `Startup.Configure` 中添加身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="4584c-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="4584c-124">如果调用 `UseAuthentication`，会注册使用之前注册的身份验证方案的中间节。</span><span class="sxs-lookup"><span data-stu-id="4584c-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="4584c-125">请在依赖于要进行身份验证的用户的所有中间件之前调用 `UseAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="4584c-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="4584c-126">如果使用终结点路由，则必须按以下顺序调用 `UseAuthentication`：</span><span class="sxs-lookup"><span data-stu-id="4584c-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="4584c-127">在 `UseRouting`之后调用，以便路由信息可用于身份验证决策。</span><span class="sxs-lookup"><span data-stu-id="4584c-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="4584c-128">在 `UseEndpoints` 之前调用，以便用户在经过身份验证后才能访问终结点。</span><span class="sxs-lookup"><span data-stu-id="4584c-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="4584c-129">身份验证概念</span><span class="sxs-lookup"><span data-stu-id="4584c-129">Authentication Concepts</span></span>

<span data-ttu-id="4584c-130">身份验证负责提供 <xref:System.Security.Claims.ClaimsPrincipal> 进行授权，以针对其进行权限决策。</span><span class="sxs-lookup"><span data-stu-id="4584c-130">Authentication is responsible for providing the <xref:System.Security.Claims.ClaimsPrincipal> for authorization to make permission decisions against.</span></span> <span data-ttu-id="4584c-131">可通过多种身份验证方案方法来选择使用哪种身份验证处理程序负责生成正确的声明集：</span><span class="sxs-lookup"><span data-stu-id="4584c-131">There are multiple authentication scheme approaches to select which authentication handler is responsible for generating the correct set of claims:</span></span>

  * <span data-ttu-id="4584c-132">[身份验证方案](xref:security/authorization/limitingidentitybyscheme)，也将在下一节中讨论。</span><span class="sxs-lookup"><span data-stu-id="4584c-132">[Authentication scheme](xref:security/authorization/limitingidentitybyscheme), also discussed in the next section.</span></span>
  * <span data-ttu-id="4584c-133">默认身份验证方案，将在下一节中讨论。</span><span class="sxs-lookup"><span data-stu-id="4584c-133">The default authentication scheme, discussed in the next section.</span></span>
  * <span data-ttu-id="4584c-134">直接设置 [HttpContext.User](xref:Microsoft.AspNetCore.Http.HttpContext.User)。</span><span class="sxs-lookup"><span data-stu-id="4584c-134">Directly set [HttpContext.User](xref:Microsoft.AspNetCore.Http.HttpContext.User).</span></span>

<span data-ttu-id="4584c-135">不会自动探测方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-135">There is no automatic probing of schemes.</span></span> <span data-ttu-id="4584c-136">如果未指定默认方案，则必须在授权属性中指定该方案，否则将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="4584c-136">If the default scheme is not specified, the scheme must be specified in the authorize attribute, otherwise, the following error is thrown:</span></span>

  <span data-ttu-id="4584c-137">InvalidOperationException：未指定任何 authenticationScheme，并且未找到 DefaultAuthenticateScheme。</span><span class="sxs-lookup"><span data-stu-id="4584c-137">InvalidOperationException: No authenticationScheme was specified, and there was no DefaultAuthenticateScheme found.</span></span> <span data-ttu-id="4584c-138">可使用 AddAuthentication(string defaultScheme) 或 AddAuthentication(Action&lt;AuthenticationOptions&gt; configureOptions) 设置默认方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-138">The default schemes can be set using either AddAuthentication(string defaultScheme) or AddAuthentication(Action&lt;AuthenticationOptions&gt; configureOptions).</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="4584c-139">身份验证方案</span><span class="sxs-lookup"><span data-stu-id="4584c-139">Authentication scheme</span></span>

<span data-ttu-id="4584c-140">[身份验证方案](xref:security/authorization/limitingidentitybyscheme)可选择使用哪种身份验证处理程序负责生成正确的声明集。</span><span class="sxs-lookup"><span data-stu-id="4584c-140">The [authentication scheme](xref:security/authorization/limitingidentitybyscheme) can select which authentication handler is responsible for generating the correct set of claims.</span></span> <span data-ttu-id="4584c-141">有关详细信息，请参阅[使用特定方案授权](xref:security/authorization/limitingidentitybyscheme)。</span><span class="sxs-lookup"><span data-stu-id="4584c-141">For more information, see [Authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span>

<span data-ttu-id="4584c-142">身份验证方案是与下列项相对应的名称：</span><span class="sxs-lookup"><span data-stu-id="4584c-142">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="4584c-143">身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="4584c-143">An authentication handler.</span></span>
* <span data-ttu-id="4584c-144">用于配置处理程序的该特定实例的选项。</span><span class="sxs-lookup"><span data-stu-id="4584c-144">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="4584c-145">方案可用作一种机制，供用户参考相关处理程序的身份验证、挑战和禁止行为。</span><span class="sxs-lookup"><span data-stu-id="4584c-145">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="4584c-146">例如，授权策略可使用方案名称来指定应使用哪种（或哪些）身份验证方案来对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4584c-146">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="4584c-147">配置身份验证时，通常是指定默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-147">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="4584c-148">除非资源请求了特定方案，否则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-148">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="4584c-149">还可：</span><span class="sxs-lookup"><span data-stu-id="4584c-149">It's also possible to:</span></span>

* <span data-ttu-id="4584c-150">指定其他默认方案供授权、挑战和禁止操作使用。</span><span class="sxs-lookup"><span data-stu-id="4584c-150">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="4584c-151">可通过[策略方案](xref:security/authentication/policyschemes)将多个方案合成一个。</span><span class="sxs-lookup"><span data-stu-id="4584c-151">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="4584c-152">身份验证处理程序</span><span class="sxs-lookup"><span data-stu-id="4584c-152">Authentication handler</span></span>

<span data-ttu-id="4584c-153">身份验证处理程序：</span><span class="sxs-lookup"><span data-stu-id="4584c-153">An authentication handler:</span></span>

* <span data-ttu-id="4584c-154">是一种实现方案行为的类型。</span><span class="sxs-lookup"><span data-stu-id="4584c-154">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="4584c-155">派生自 <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> 或 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>。</span><span class="sxs-lookup"><span data-stu-id="4584c-155">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="4584c-156">具有对用户进行身份验证的主要责任。</span><span class="sxs-lookup"><span data-stu-id="4584c-156">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="4584c-157">根据身份验证方案的配置和传入的请求上下文，身份验证处理程序：</span><span class="sxs-lookup"><span data-stu-id="4584c-157">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="4584c-158">构造表示用户身份的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> 对象（若身份验证成功）。</span><span class="sxs-lookup"><span data-stu-id="4584c-158">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="4584c-159">返回“无结果”或“失败”（若身份验证失败）。</span><span class="sxs-lookup"><span data-stu-id="4584c-159">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="4584c-160">具有用于挑战和禁止操作的方法，供用户在下述情况下访问资源时使用：</span><span class="sxs-lookup"><span data-stu-id="4584c-160">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="4584c-161">他们未获得访问授权（禁止）。</span><span class="sxs-lookup"><span data-stu-id="4584c-161">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="4584c-162">他们未经过身份验证（挑战）。</span><span class="sxs-lookup"><span data-stu-id="4584c-162">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="4584c-163">Authenticate</span><span class="sxs-lookup"><span data-stu-id="4584c-163">Authenticate</span></span>

<span data-ttu-id="4584c-164">身份验证方案的身份验证操作负责根据请求上下文构造用户的身份。</span><span class="sxs-lookup"><span data-stu-id="4584c-164">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="4584c-165">它会返回一个 <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>指示身份验证是否成功；若成功，则还在身份验证票证中指示用户的身份。</span><span class="sxs-lookup"><span data-stu-id="4584c-165">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="4584c-166">请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="4584c-166">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="4584c-167">身份验证示例包括：</span><span class="sxs-lookup"><span data-stu-id="4584c-167">Authenticate examples include:</span></span>

* <span data-ttu-id="4584c-168">根据 cookie 构造用户身份的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-168">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="4584c-169">对 JWT 持有者令牌进行反序列化和验证以构造用户身份的 JWT 持有者方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-169">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="4584c-170">挑战</span><span class="sxs-lookup"><span data-stu-id="4584c-170">Challenge</span></span>

<span data-ttu-id="4584c-171">当未经身份验证的用户请求要求身份验证的终结点时，授权会发起身份验证挑战。</span><span class="sxs-lookup"><span data-stu-id="4584c-171">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="4584c-172">例如，当匿名用户请求受限资源或单击登录链接时，会引发身份验证挑战。</span><span class="sxs-lookup"><span data-stu-id="4584c-172">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="4584c-173">授权会使用指定的身份验证方案发起挑战；如果未指定任何方案，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-173">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="4584c-174">请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="4584c-174">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="4584c-175">身份验证挑战示例包括：</span><span class="sxs-lookup"><span data-stu-id="4584c-175">Authentication challenge examples include:</span></span>

* <span data-ttu-id="4584c-176">将用户重定向到登录页面的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-176">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="4584c-177">返回具有 `www-authenticate: bearer` 标头的 401 结果的 JWT 持有者方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-177">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="4584c-178">挑战操作应告知用户要使用哪种身份验证机制来访问所请求的资源。</span><span class="sxs-lookup"><span data-stu-id="4584c-178">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="4584c-179">禁止</span><span class="sxs-lookup"><span data-stu-id="4584c-179">Forbid</span></span>

<span data-ttu-id="4584c-180">当已经过身份验证的用户尝试访问其无权访问的资源时，授权会调用身份验证方案的禁止操作。</span><span class="sxs-lookup"><span data-stu-id="4584c-180">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="4584c-181">请参阅 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="4584c-181">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="4584c-182">身份验证禁止示例包括：</span><span class="sxs-lookup"><span data-stu-id="4584c-182">Authentication forbid examples include:</span></span>
* <span data-ttu-id="4584c-183">将用户重定向到表示访问遭禁的页面的 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-183">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="4584c-184">返回 403 结果的 JWT 持有者方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-184">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="4584c-185">重定向到用户可请求资源访问权限的页面的自定义身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-185">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="4584c-186">用户可通过禁止操作知道：</span><span class="sxs-lookup"><span data-stu-id="4584c-186">A forbid action can let the user know:</span></span>

* <span data-ttu-id="4584c-187">他们已经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="4584c-187">They are authenticated.</span></span>
* <span data-ttu-id="4584c-188">他们无权访问所请求的资源。</span><span class="sxs-lookup"><span data-stu-id="4584c-188">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="4584c-189">请查看以下链接，了解挑战与禁止之间区别：</span><span class="sxs-lookup"><span data-stu-id="4584c-189">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="4584c-190">[使用操作资源处理程序的挑战和禁止](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)。</span><span class="sxs-lookup"><span data-stu-id="4584c-190">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="4584c-191">[挑战与禁止之间的区别](xref:security/authorization/secure-data#challenge)。</span><span class="sxs-lookup"><span data-stu-id="4584c-191">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="4584c-192">每个租户的身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="4584c-192">Authentication providers per tenant</span></span>

<span data-ttu-id="4584c-193">ASP.NET Core 框架没有用于多租户身份验证的内置解决方案。</span><span class="sxs-lookup"><span data-stu-id="4584c-193">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="4584c-194">虽然客户当然可以使用内置功能编写一个解决方案，但建议客户查看 [Orchard Core](https://www.orchardcore.net/) 以实现此目的。</span><span class="sxs-lookup"><span data-stu-id="4584c-194">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="4584c-195">Orchard Core 是：</span><span class="sxs-lookup"><span data-stu-id="4584c-195">Orchard Core is:</span></span>

* <span data-ttu-id="4584c-196">使用 ASP.NET Core 生成的开放源代码模块和多租户应用框架。</span><span class="sxs-lookup"><span data-stu-id="4584c-196">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="4584c-197">基于该应用框架生成的内容管理系统 (CMS)。</span><span class="sxs-lookup"><span data-stu-id="4584c-197">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="4584c-198">请参阅 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 源，了解每个租户的身份验证提供程序的示例。</span><span class="sxs-lookup"><span data-stu-id="4584c-198">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4584c-199">其他资源</span><span class="sxs-lookup"><span data-stu-id="4584c-199">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="4584c-200">全球要求经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="4584c-200">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
* [<span data-ttu-id="4584c-201">GitHub 上关于使用多个身份验证方案的问题</span><span class="sxs-lookup"><span data-stu-id="4584c-201">GitHub issue on using multiple authentication schemes</span></span>](https://github.com/dotnet/aspnetcore/issues/26002)
