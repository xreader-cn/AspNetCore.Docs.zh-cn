---
title: ':::no-loc(cookie):::不使用身份验证:::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: '了解如何在 :::no-loc(cookie)::: 不使用身份验证的情况下使用 :::no-loc(ASP.NET Core Identity)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061348"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="5d9bf-103">:::no-loc(cookie):::不使用身份验证:::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="5d9bf-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="5d9bf-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d9bf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d9bf-105">:::no-loc(ASP.NET Core Identity)::: 是一个完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="5d9bf-106">但是， :::no-loc(cookie)::: 不能使用基于的身份验证提供程序 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="5d9bf-107">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="5d9bf-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5d9bf-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5d9bf-109">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="5d9bf-110">使用 **电子邮件** 地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="5d9bf-111">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs* 文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="5d9bf-112">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="5d9bf-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="5d9bf-113">Configuration</span></span>

<span data-ttu-id="5d9bf-114">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="5d9bf-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="5d9bf-116">`AuthenticationScheme` 如果有多个 :::no-loc(cookie)::: 身份验证实例，并且你想要 [使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="5d9bf-117">将设置 `AuthenticationScheme` 为[ :::no-loc(Cookie)::: AuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)为方案提供值 " :::no-loc(Cookie)::: s"。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="5d9bf-118">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="5d9bf-119">应用的身份验证方案不同于应用的 :::no-loc(cookie)::: 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="5d9bf-120">当 :::no-loc(cookie)::: 未提供身份验证方案时 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ，它将使用 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` ( " :::no-loc(Cookie)::: s" ) 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="5d9bf-121">:::no-loc(cookie):::默认情况下，身份验证的 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="5d9bf-122">:::no-loc(cookie):::当站点访问者未同意数据收集时，允许使用身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="5d9bf-123">有关详细信息，请参阅 <xref:security/gdpr#essential-:::no-loc(cookie):::s>。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="5d9bf-124">在中 `Startup.Configure` ，调用 `UseAuthentication` 并 `UseAuthorization` 设置 `HttpContext.User` 属性并为请求运行授权中间件。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="5d9bf-125">`UseAuthentication` `UseAuthorization` 在调用之前调用和方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="5d9bf-126"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="5d9bf-127">在 `:::no-loc(Cookie):::AuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="5d9bf-128">:::no-loc(Cookie)::: 策略中间件</span><span class="sxs-lookup"><span data-stu-id="5d9bf-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="5d9bf-129">[ :::no-loc(Cookie)::: 策略中间件](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)启用 :::no-loc(cookie)::: 策略功能。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="5d9bf-130">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="5d9bf-131">使用 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> 提供给 :::no-loc(Cookie)::: 策略中间件来控制 :::no-loc(cookie)::: :::no-loc(cookie)::: 在 :::no-loc(cookie)::: 追加或删除时处理和挂钩处理处理程序的全局特性。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="5d9bf-132">默认 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="5d9bf-133">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="5d9bf-134">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它会提升 :::no-loc(cookie)::: 其他不依赖于跨源请求处理的应用类型的安全级别。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="5d9bf-135">的 :::no-loc(Cookie)::: 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `:::no-loc(Cookie):::.SameSite` ， `:::no-loc(Cookie):::AuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="5d9bf-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5d9bf-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="5d9bf-137">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="5d9bf-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="5d9bf-138">结果 :::no-loc(Cookie)::: 。SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="5d9bf-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="5d9bf-139">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-139">SameSiteMode.None</span></span>     | <span data-ttu-id="5d9bf-140">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-140">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-141">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-142">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-143">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-143">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-144">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-145">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5d9bf-146">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="5d9bf-147">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-147">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-148">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-149">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-150">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-151">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-152">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5d9bf-153">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="5d9bf-154">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-154">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-155">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-156">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-157">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="5d9bf-158">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="5d9bf-159">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="5d9bf-160">创建身份验证 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5d9bf-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="5d9bf-161">若要创建 :::no-loc(cookie)::: 保存用户信息，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="5d9bf-162">将序列化用户信息并将其存储在中 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="5d9bf-163"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="5d9bf-164">`SignInAsync` 创建一个加密的 :::no-loc(cookie)::: ，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="5d9bf-165">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="5d9bf-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 默认情况下，仅在几个特定路径上使用，例如登录路径和注销路径。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="5d9bf-167">有关详细信息，请参阅[ :::no-loc(Cookie)::: AuthenticationHandler 源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334)。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="5d9bf-168">ASP.NET Core 的 [数据保护](xref:security/data-protection/using-data-protection) 系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="5d9bf-169">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请 [将数据保护配置](xref:security/data-protection/configuration/overview) 为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="5d9bf-170">注销</span><span class="sxs-lookup"><span data-stu-id="5d9bf-170">Sign out</span></span>

<span data-ttu-id="5d9bf-171">若要注销当前用户并将其删除 :::no-loc(cookie)::: ，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="5d9bf-172">如果 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (或 " :::no-loc(Cookie)::: s" ) 未用作方案 (例如 "Contoso :::no-loc(Cookie)::: " ) ，请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="5d9bf-173">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="5d9bf-174">当浏览器关闭时，它将自动删除基于会话 :::no-loc(cookie)::: 的 (非持久 :::no-loc(cookie):::) ，但 :::no-loc(cookie)::: 当关闭单个选项卡时，将不会清除任何。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="5d9bf-175">未向服务器通知选项卡或浏览器关闭事件。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="5d9bf-176">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="5d9bf-176">React to back-end changes</span></span>

<span data-ttu-id="5d9bf-177">一旦 :::no-loc(cookie)::: 创建， :::no-loc(cookie)::: 就是标识的单个源。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="5d9bf-178">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="5d9bf-179">应用的 :::no-loc(cookie)::: 身份验证系统将继续根据身份验证处理请求 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="5d9bf-180">只要身份验证有效，用户就会保持登录到应用 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="5d9bf-181"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 :::no-loc(cookie)::: 标识验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="5d9bf-182">验证 :::no-loc(cookie)::: 每个请求是否会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="5d9bf-183">验证的一种方法 :::no-loc(cookie)::: 是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="5d9bf-184">如果自用户颁发以来数据库尚未发生更改 :::no-loc(cookie)::: ，则无需重新对用户进行身份验证，前提 :::no-loc(cookie)::: 是用户仍有效。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="5d9bf-185">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="5d9bf-186">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="5d9bf-187">为了使 :::no-loc(cookie)::: 数据库根据值发生更改时失效 `LastChanged` ，请 :::no-loc(cookie)::: 使用 `LastChanged` 包含数据库中的当前值的声明创建 `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="5d9bf-188">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="5d9bf-189">下面是的示例实现 `:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="5d9bf-190">:::no-loc(cookie):::在方法中注册服务期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5d9bf-191">提供类的 [作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `Custom:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="5d9bf-192">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="5d9bf-193">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="5d9bf-194">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="5d9bf-195">验证 :::no-loc(cookie)::: 每个请求的所有用户的身份验证可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="5d9bf-196">持久性 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5d9bf-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="5d9bf-197">你可能希望在 :::no-loc(cookie)::: 浏览器会话之间保持。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="5d9bf-198">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="5d9bf-199">下面的代码片段将创建一个标识，并 :::no-loc(cookie)::: 在浏览器闭包中置对应。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="5d9bf-200">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="5d9bf-201">如果 :::no-loc(cookie)::: 浏览器关闭时过期，浏览器会在 :::no-loc(cookie)::: 重新启动后将其清除。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="5d9bf-202">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="5d9bf-203">绝对 :::no-loc(cookie)::: 过期</span><span class="sxs-lookup"><span data-stu-id="5d9bf-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="5d9bf-204">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="5d9bf-205">若要创建持久 :::no-loc(cookie)::: ， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="5d9bf-206">否则， :::no-loc(cookie)::: 创建时使用基于会话的生存期，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="5d9bf-207">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="5d9bf-208">下面的代码片段将创建一个标识，并将 :::no-loc(cookie)::: 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="5d9bf-209">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5d9bf-210">:::no-loc(ASP.NET Core Identity)::: 是一个完整的全功能身份验证提供程序，用于创建和维护登录名。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="5d9bf-211">但是， :::no-loc(cookie)::: 不能使用基于的身份验证提供程序 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="5d9bf-212">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="5d9bf-213">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5d9bf-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5d9bf-214">出于演示目的，在示例应用程序中，假设用户（Maria Rodriguez）的用户帐户已硬编码到应用中。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="5d9bf-215">使用 **电子邮件** 地址 `maria.rodriguez@contoso.com` 和任何密码来登录用户。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="5d9bf-216">用户通过 `AuthenticateUser` *页面/帐户/登录名. .cs* 文件中的方法进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="5d9bf-217">在实际的示例中，用户将对数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="5d9bf-218">Configuration</span><span class="sxs-lookup"><span data-stu-id="5d9bf-218">Configuration</span></span>

<span data-ttu-id="5d9bf-219">如果应用程序不使用 [AspNetCore 元包](xref:fundamentals/metapackage-app)，请在项目文件中为 AspNetCore 创建包引用 [。 :::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) 包。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="5d9bf-220">在 `Startup.ConfigureServices` 方法中，创建具有和方法的身份验证中间件服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="5d9bf-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 传递以 `AddAuthentication` 设置应用程序的默认身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="5d9bf-222">`AuthenticationScheme` 如果有多个 :::no-loc(cookie)::: 身份验证实例，并且你想要 [使用特定方案进行授权](xref:security/authorization/limitingidentitybyscheme)，则会很有用。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="5d9bf-223">将设置 `AuthenticationScheme` 为[ :::no-loc(Cookie)::: AuthenticationDefaults。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)为方案提供值 " :::no-loc(Cookie)::: s"。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="5d9bf-224">可以提供任何用于区分方案的字符串值。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="5d9bf-225">应用的身份验证方案不同于应用的 :::no-loc(cookie)::: 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="5d9bf-226">当 :::no-loc(cookie)::: 未提供身份验证方案时 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ，它将使用 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` ( " :::no-loc(Cookie)::: s" ) 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="5d9bf-227">:::no-loc(cookie):::默认情况下，身份验证的 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="5d9bf-228">:::no-loc(cookie):::当站点访问者未同意数据收集时，允许使用身份验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="5d9bf-229">有关详细信息，请参阅 <xref:security/gdpr#essential-:::no-loc(cookie):::s>。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="5d9bf-230">在 `Startup.Configure` 方法中，调用 `UseAuthentication` 方法以调用设置属性的身份验证中间件 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5d9bf-231">`UseAuthentication`在调用或之前调用 `UseMvcWithDefaultRoute` 方法 `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="5d9bf-232"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>类用于配置身份验证提供程序选项。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="5d9bf-233">在 `:::no-loc(Cookie):::AuthenticationOptions` 服务配置中设置，以便在方法中进行身份验证 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="5d9bf-234">:::no-loc(Cookie)::: 策略中间件</span><span class="sxs-lookup"><span data-stu-id="5d9bf-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="5d9bf-235">[ :::no-loc(Cookie)::: 策略中间件](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)启用 :::no-loc(cookie)::: 策略功能。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="5d9bf-236">将中间件添加到应用处理管道是区分顺序的， &mdash; 它仅影响在管道中注册的下游组件。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="5d9bf-237">使用 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> 提供给 :::no-loc(Cookie)::: 策略中间件来控制 :::no-loc(cookie)::: :::no-loc(cookie)::: 在 :::no-loc(cookie)::: 追加或删除时处理和挂钩处理处理程序的全局特性。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="5d9bf-238">默认 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 值为 `SameSiteMode.Lax` 允许 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="5d9bf-239">若要严格地强制执行同一站点策略 `SameSiteMode.Strict` ，请设置 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="5d9bf-240">尽管此设置会中断 OAuth2 和其他跨源身份验证方案，但它会提升 :::no-loc(cookie)::: 其他不依赖于跨源请求处理的应用类型的安全级别。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="5d9bf-241">的 :::no-loc(Cookie)::: 策略中间件设置会 `MinimumSameSitePolicy` 影响中的设置 `:::no-loc(Cookie):::.SameSite` ， `:::no-loc(Cookie):::AuthenticationOptions` 具体取决于下面的矩阵。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="5d9bf-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5d9bf-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="5d9bf-243">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="5d9bf-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="5d9bf-244">结果 :::no-loc(Cookie)::: 。SameSite 设置</span><span class="sxs-lookup"><span data-stu-id="5d9bf-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="5d9bf-245">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-245">SameSiteMode.None</span></span>     | <span data-ttu-id="5d9bf-246">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-246">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-247">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-248">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-249">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-249">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-250">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-251">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5d9bf-252">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="5d9bf-253">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-253">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-254">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-255">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-256">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-257">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-258">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5d9bf-259">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="5d9bf-260">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-260">SameSiteMode.None</span></span><br><span data-ttu-id="5d9bf-261">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="5d9bf-262">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="5d9bf-263">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="5d9bf-264">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="5d9bf-265">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5d9bf-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="5d9bf-266">创建身份验证 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5d9bf-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="5d9bf-267">若要创建 :::no-loc(cookie)::: 保存用户信息，请构造 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="5d9bf-268">将序列化用户信息并将其存储在中 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="5d9bf-269"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>使用任何所需的 <xref:System.Security.Claims.Claim> 和调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 来登录用户：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="5d9bf-270">`SignInAsync` 创建一个加密的 :::no-loc(cookie)::: ，并将其添加到当前响应中。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="5d9bf-271">如果 `AuthenticationScheme` 未指定，则使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="5d9bf-272">ASP.NET Core 的 [数据保护](xref:security/data-protection/using-data-protection) 系统用于加密。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="5d9bf-273">对于托管在多台计算机上的应用程序、跨应用程序或使用 web 场进行负载平衡，请 [将数据保护配置](xref:security/data-protection/configuration/overview) 为使用相同的密钥环和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="5d9bf-274">注销</span><span class="sxs-lookup"><span data-stu-id="5d9bf-274">Sign out</span></span>

<span data-ttu-id="5d9bf-275">若要注销当前用户并将其删除 :::no-loc(cookie)::: ，请调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="5d9bf-276">如果 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (或 " :::no-loc(Cookie)::: s" ) 未用作方案 (例如 "Contoso :::no-loc(Cookie)::: " ) ，请提供配置身份验证提供程序时所使用的方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="5d9bf-277">否则，将使用默认方案。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="5d9bf-278">对后端更改作出反应</span><span class="sxs-lookup"><span data-stu-id="5d9bf-278">React to back-end changes</span></span>

<span data-ttu-id="5d9bf-279">一旦 :::no-loc(cookie)::: 创建， :::no-loc(cookie)::: 就是标识的单个源。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="5d9bf-280">如果在后端系统中禁用用户帐户：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="5d9bf-281">应用的 :::no-loc(cookie)::: 身份验证系统将继续根据身份验证处理请求 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="5d9bf-282">只要身份验证有效，用户就会保持登录到应用 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="5d9bf-283"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>事件可用于截获和重写 :::no-loc(cookie)::: 标识验证。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="5d9bf-284">验证 :::no-loc(cookie)::: 每个请求是否会降低吊销用户访问应用的风险。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="5d9bf-285">验证的一种方法 :::no-loc(cookie)::: 是基于跟踪用户数据库更改的时间。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="5d9bf-286">如果自用户颁发以来数据库尚未发生更改 :::no-loc(cookie)::: ，则无需重新对用户进行身份验证，前提 :::no-loc(cookie)::: 是用户仍有效。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="5d9bf-287">在示例应用中，数据库在中实现 `IUserRepository` 并存储一个 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="5d9bf-288">当用户在数据库中更新时，该值将 `LastChanged` 设置为当前时间。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="5d9bf-289">为了使 :::no-loc(cookie)::: 数据库根据值发生更改时失效 `LastChanged` ，请 :::no-loc(cookie)::: 使用 `LastChanged` 包含数据库中的当前值的声明创建 `LastChanged` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="5d9bf-290">若要为事件实现替代 `ValidatePrincipal` ，请在派生自的类中编写具有以下签名的方法 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="5d9bf-291">下面是的示例实现 `:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="5d9bf-292">:::no-loc(cookie):::在方法中注册服务期间注册事件实例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5d9bf-293">提供类的 [作用域服务注册](xref:fundamentals/dependency-injection#service-lifetimes) `Custom:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="5d9bf-294">请考虑这样一种情况：用户的名称更新为 &mdash; 不影响安全的决策。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="5d9bf-295">如果要以非破坏性的更新用户主体，请调用， `context.ReplacePrincipal` 并将 `context.ShouldRenew` 属性设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="5d9bf-296">此处所述的方法在每个请求时触发。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="5d9bf-297">验证 :::no-loc(cookie)::: 每个请求的所有用户的身份验证可能会导致应用程序的性能下降。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="5d9bf-298">持久性 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5d9bf-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="5d9bf-299">你可能希望在 :::no-loc(cookie)::: 浏览器会话之间保持。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="5d9bf-300">仅当登录或类似机制上出现 "记住我" 复选框时，才应启用此持久性。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="5d9bf-301">下面的代码片段将创建一个标识，并 :::no-loc(cookie)::: 在浏览器闭包中置对应。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="5d9bf-302">预先配置的任何可调过期设置都将起作用。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="5d9bf-303">如果 :::no-loc(cookie)::: 浏览器关闭时过期，浏览器会在 :::no-loc(cookie)::: 重新启动后将其清除。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="5d9bf-304">设置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 为 `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="5d9bf-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="5d9bf-305">绝对 :::no-loc(cookie)::: 过期</span><span class="sxs-lookup"><span data-stu-id="5d9bf-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="5d9bf-306">绝对过期时间可以设置为 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="5d9bf-307">若要创建持久 :::no-loc(cookie)::: ， `IsPersistent` 还必须设置。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="5d9bf-308">否则， :::no-loc(cookie)::: 创建时使用基于会话的生存期，并且可能会在它所包含的身份验证票证之前或之后过期。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="5d9bf-309">设置后， `ExpiresUtc` 它将覆盖的 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 选项的值 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> （如果已设置）。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="5d9bf-310">下面的代码片段将创建一个标识，并将 :::no-loc(cookie)::: 持续20分钟。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="5d9bf-311">这将忽略以前配置的任何可调过期设置。</span><span class="sxs-lookup"><span data-stu-id="5d9bf-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5d9bf-312">其他资源</span><span class="sxs-lookup"><span data-stu-id="5d9bf-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="5d9bf-313">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="5d9bf-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
