---
title: SameSite
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用 SameSite cookie
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2019
uid: security/samesite
ms.openlocfilehash: 91c69c4caf0644c1f40750233175ddb4c02d7cfe
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717435"
---
# <a name="working-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="51c5f-103">在 ASP.NET Core 中使用 SameSite cookie</span><span class="sxs-lookup"><span data-stu-id="51c5f-103">Working with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="51c5f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51c5f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51c5f-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07)是一种[IETF](https://ietf.org/about/)草案，旨在针对跨站点请求伪造（CSRF）攻击提供某些防护。</span><span class="sxs-lookup"><span data-stu-id="51c5f-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) is an [IETF](https://ietf.org/about/) draft designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="51c5f-106">[SameSite 2019 草案](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)：</span><span class="sxs-lookup"><span data-stu-id="51c5f-106">The [SameSite 2019 draft](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span></span>

* <span data-ttu-id="51c5f-107">默认情况下，将 cookie 视为 `SameSite=Lax`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-107">Treats cookies as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="51c5f-108">为了启用跨站点传递而显式断言 `SameSite=None` 的 cookie 应标记为 `Secure`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-108">States cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span>

<span data-ttu-id="51c5f-109">`Lax` 适用于大多数应用 cookie。</span><span class="sxs-lookup"><span data-stu-id="51c5f-109">`Lax` works for most app cookies.</span></span> <span data-ttu-id="51c5f-110">某些形式的身份验证，例如[OpenID connect](https://openid.net/connect/) （OIDC）和[ws-federation](https://auth0.com/docs/protocols/ws-fed)默认为基于 POST 的重定向。</span><span class="sxs-lookup"><span data-stu-id="51c5f-110">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="51c5f-111">基于后期的重定向会触发 SameSite 浏览器保护，因此，对这些组件禁用了 SameSite。</span><span class="sxs-lookup"><span data-stu-id="51c5f-111">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="51c5f-112">由于请求的流动方式不同，大多数[OAuth](https://oauth.net/)登录名不受影响。</span><span class="sxs-lookup"><span data-stu-id="51c5f-112">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="51c5f-113">`None` 参数会导致实现之前2016草案标准（例如，iOS 12）的客户端的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="51c5f-113">The `None` parameter causes compatibility problems with clients that implemented the prior 2016 draft standard (for example, iOS 12).</span></span> <span data-ttu-id="51c5f-114">请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-114">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="51c5f-115">发出 cookie 的每个 ASP.NET Core 组件都需要确定 SameSite 是否合适。</span><span class="sxs-lookup"><span data-stu-id="51c5f-115">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="api-usage-with-samesite"></a><span data-ttu-id="51c5f-116">使用 SameSite 的 API 用法</span><span class="sxs-lookup"><span data-stu-id="51c5f-116">API usage with SameSite</span></span>

<span data-ttu-id="51c5f-117">[Httpcontext.current](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)默认值为 `Unspecified`，这意味着，不会向 cookie 中添加 SameSite 属性，客户端将使用其默认行为（对于新浏览器而言，对于旧浏览器则为 "无"）。</span><span class="sxs-lookup"><span data-stu-id="51c5f-117">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="51c5f-118">下面的代码演示如何将 cookie SameSite 值更改为 `SameSiteMode.Lax`：</span><span class="sxs-lookup"><span data-stu-id="51c5f-118">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="51c5f-119">发出 cookie 的所有 ASP.NET Core 组件都用适用于其方案的设置替代前面的默认值。</span><span class="sxs-lookup"><span data-stu-id="51c5f-119">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="51c5f-120">先前重写的默认值尚未更改。</span><span class="sxs-lookup"><span data-stu-id="51c5f-120">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="51c5f-121">组件</span><span class="sxs-lookup"><span data-stu-id="51c5f-121">Component</span></span> | <span data-ttu-id="51c5f-122">票证</span><span class="sxs-lookup"><span data-stu-id="51c5f-122">cookie</span></span> | <span data-ttu-id="51c5f-123">默认值</span><span class="sxs-lookup"><span data-stu-id="51c5f-123">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="51c5f-124">SessionOptions</span><span class="sxs-lookup"><span data-stu-id="51c5f-124">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="51c5f-125">CookieTempDataProviderOptions</span><span class="sxs-lookup"><span data-stu-id="51c5f-125">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="51c5f-126">AntiforgeryOptions</span><span class="sxs-lookup"><span data-stu-id="51c5f-126">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="51c5f-127">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="51c5f-127">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="51c5f-128">Cookieauthenticationoptions.authenticationtype</span><span class="sxs-lookup"><span data-stu-id="51c5f-128">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="51c5f-129">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="51c5f-129">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="51c5f-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="51c5f-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="51c5f-131">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="51c5f-131">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="51c5f-132">Httpcontext.current。追加</span><span class="sxs-lookup"><span data-stu-id="51c5f-132">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="51c5f-133">ASP.NET Core 3.1 和更高版本提供了以下 SameSite 支持：</span><span class="sxs-lookup"><span data-stu-id="51c5f-133">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="51c5f-134">重新定义发出 `SameSiteMode.None` 的行为 `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="51c5f-134">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="51c5f-135">将新值添加 `SameSiteMode.Unspecified` 以省略 SameSite 属性。</span><span class="sxs-lookup"><span data-stu-id="51c5f-135">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="51c5f-136">所有 cookie Api 默认为 `Unspecified`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-136">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="51c5f-137">某些使用 cookie 的组件会将值设置得更加特定于其应用场景。</span><span class="sxs-lookup"><span data-stu-id="51c5f-137">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="51c5f-138">有关示例，请参阅上表。</span><span class="sxs-lookup"><span data-stu-id="51c5f-138">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51c5f-139">在 ASP.NET Core 3.0 及更高版本中，SameSite 默认值已更改，以避免与客户端默认值不一致发生冲突。</span><span class="sxs-lookup"><span data-stu-id="51c5f-139">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="51c5f-140">以下 Api 已将默认值从 `SameSiteMode.Lax ` 更改为 `-1`，以避免发出这些 cookie 的 SameSite 属性：</span><span class="sxs-lookup"><span data-stu-id="51c5f-140">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="51c5f-141">与[HttpContext](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)一起使用的 <xref:Microsoft.AspNetCore.Http.CookieOptions></span><span class="sxs-lookup"><span data-stu-id="51c5f-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="51c5f-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder> 用作 `CookieOptions` 的工厂</span><span class="sxs-lookup"><span data-stu-id="51c5f-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="51c5f-143">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="51c5f-143">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="51c5f-144">历史记录和更改</span><span class="sxs-lookup"><span data-stu-id="51c5f-144">History and changes</span></span>

<span data-ttu-id="51c5f-145">SameSite 支持在2.0 中第一次 ASP.NET Core 实现，使用[2016 草案标准](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-145">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="51c5f-146">2016标准已选择加入。</span><span class="sxs-lookup"><span data-stu-id="51c5f-146">The 2016 standard was opt-in.</span></span> <span data-ttu-id="51c5f-147">ASP.NET Core 选择在默认情况下 `Lax` 的几个 cookie。</span><span class="sxs-lookup"><span data-stu-id="51c5f-147">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="51c5f-148">在遇到身份验证的几个[问题](https://github.com/aspnet/Announcements/issues/318)后，大多数 SameSite 使用已[禁用](https://github.com/aspnet/Announcements/issues/348)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-148">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="51c5f-149">2019年11月发布了修补程序，从2016标准版更新为2019标准。</span><span class="sxs-lookup"><span data-stu-id="51c5f-149">Patches were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="51c5f-150">[SameSite 规范的2019草案](https://github.com/aspnet/Announcements/issues/390)：</span><span class="sxs-lookup"><span data-stu-id="51c5f-150">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="51c5f-151">**不**向后兼容2016草案。</span><span class="sxs-lookup"><span data-stu-id="51c5f-151">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="51c5f-152">有关详细信息，请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-152">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="51c5f-153">指定默认情况下将 cookie 视为 `SameSite=Lax`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-153">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="51c5f-154">指定显式断言 `SameSite=None` 以便启用跨站点传递的 cookie 应标记为 `Secure`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-154">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="51c5f-155">`None` 是选择退出的新项。</span><span class="sxs-lookup"><span data-stu-id="51c5f-155">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="51c5f-156">为 ASP.NET Core 2.1、2.2 和3.0 颁发的修补程序支持。</span><span class="sxs-lookup"><span data-stu-id="51c5f-156">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="51c5f-157">ASP.NET Core 3.1 具有附加的 SameSite 支持。</span><span class="sxs-lookup"><span data-stu-id="51c5f-157">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="51c5f-158">默认[情况下，计划](https://chromestatus.com/feature/5088147346030592)在[2020 年2月](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)启用。</span><span class="sxs-lookup"><span data-stu-id="51c5f-158">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="51c5f-159">浏览器已开始在2019中移动到此标准。</span><span class="sxs-lookup"><span data-stu-id="51c5f-159">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="51c5f-160">受从 2016 SameSite 草案标准到2019草案标准的更改影响的 Api</span><span class="sxs-lookup"><span data-stu-id="51c5f-160">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="51c5f-161">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="51c5f-161">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="51c5f-162">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="51c5f-162">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="51c5f-163">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="51c5f-163">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="51c5f-164">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="51c5f-164">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="51c5f-165">支持旧版浏览器</span><span class="sxs-lookup"><span data-stu-id="51c5f-165">Supporting older browsers</span></span>

<span data-ttu-id="51c5f-166">2016 SameSite 标准规定，未知值必须被视为 `SameSite=Strict` 值。</span><span class="sxs-lookup"><span data-stu-id="51c5f-166">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="51c5f-167">从支持 2016 SameSite 标准的旧版浏览器访问的应用可能会在收到值为 "`None`" 的 SameSite 属性时中断。</span><span class="sxs-lookup"><span data-stu-id="51c5f-167">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="51c5f-168">如果 Web 应用要支持较旧的浏览器，则必须实现浏览器检测。</span><span class="sxs-lookup"><span data-stu-id="51c5f-168">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="51c5f-169">ASP.NET Core 不会实现浏览器检测，因为用户代理值非常稳定且频繁更改。</span><span class="sxs-lookup"><span data-stu-id="51c5f-169">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="51c5f-170"><xref:Microsoft.AspNetCore.CookiePolicy> 中的扩展点允许插入特定于用户代理的逻辑。</span><span class="sxs-lookup"><span data-stu-id="51c5f-170">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="51c5f-171">在 `Startup.Configure`中，添加调用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> 的代码，然后调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或*任何*写入 cookie 的方法：</span><span class="sxs-lookup"><span data-stu-id="51c5f-171">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="51c5f-172">在 `Startup.ConfigureServices`中，添加类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="51c5f-172">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="51c5f-173">在前面的示例中，`MyUserAgentDetectionLib.DisallowsSameSiteNone` 是一个用户提供的库，用于检测用户代理是否不支持 SameSite `None`：</span><span class="sxs-lookup"><span data-stu-id="51c5f-173">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="51c5f-174">下面的代码演示 `DisallowsSameSiteNone` 方法示例：</span><span class="sxs-lookup"><span data-stu-id="51c5f-174">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="51c5f-175">以下代码仅用于演示：</span><span class="sxs-lookup"><span data-stu-id="51c5f-175">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="51c5f-176">不应将其视为已完成。</span><span class="sxs-lookup"><span data-stu-id="51c5f-176">It should not be considered complete.</span></span>
> * <span data-ttu-id="51c5f-177">它不维护或不受支持。</span><span class="sxs-lookup"><span data-stu-id="51c5f-177">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="51c5f-178">测试应用的 SameSite 问题</span><span class="sxs-lookup"><span data-stu-id="51c5f-178">Test apps for SameSite problems</span></span>

<span data-ttu-id="51c5f-179">与远程站点（如通过第三方登录）交互的应用需要：</span><span class="sxs-lookup"><span data-stu-id="51c5f-179">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="51c5f-180">测试多个浏览器上的交互。</span><span class="sxs-lookup"><span data-stu-id="51c5f-180">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="51c5f-181">应用本文档中讨论的[CookiePolicy 浏览器检测和缓解措施](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-181">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="51c5f-182">使用可选择新的 SameSite 行为的客户端版本测试 web 应用。</span><span class="sxs-lookup"><span data-stu-id="51c5f-182">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="51c5f-183">Chrome、Firefox 和 Chromium Edge 都具有可用于测试的新的可选功能标志。</span><span class="sxs-lookup"><span data-stu-id="51c5f-183">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="51c5f-184">应用应用 SameSite 修补程序后，请对其进行测试，使其与早期版本的客户端版本（尤其是 Safari）</span><span class="sxs-lookup"><span data-stu-id="51c5f-184">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="51c5f-185">有关详细信息，请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-185">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="51c5f-186">用 Chrome 测试</span><span class="sxs-lookup"><span data-stu-id="51c5f-186">Test with Chrome</span></span>

<span data-ttu-id="51c5f-187">Chrome 78 + 提供了令人误解的结果，因为它具有临时的缓解措施。</span><span class="sxs-lookup"><span data-stu-id="51c5f-187">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="51c5f-188">Chrome 78 + 暂时缓解功能允许 cookie 不到两分钟。</span><span class="sxs-lookup"><span data-stu-id="51c5f-188">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="51c5f-189">已启用适当测试标志的 Chrome 76 或77提供更准确的结果。</span><span class="sxs-lookup"><span data-stu-id="51c5f-189">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="51c5f-190">若要测试新的 SameSite 行为，请切换 `chrome://flags/#same-site-by-default-cookies`**启用**。</span><span class="sxs-lookup"><span data-stu-id="51c5f-190">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="51c5f-191">旧版本的 Chrome （75及更低版本）将报告为失败，并出现新的 `None` 设置。</span><span class="sxs-lookup"><span data-stu-id="51c5f-191">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="51c5f-192">请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-192">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="51c5f-193">Google 不会使旧版 chrome 版本可用。</span><span class="sxs-lookup"><span data-stu-id="51c5f-193">Google does not make older chrome versions available.</span></span> <span data-ttu-id="51c5f-194">遵循[下载 Chromium](https://www.chromium.org/getting-involved/download-chromium)中的说明来测试旧版 Chrome。</span><span class="sxs-lookup"><span data-stu-id="51c5f-194">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="51c5f-195">不要从通过搜索旧版 chrome 提供的**链接下载 chrome** 。</span><span class="sxs-lookup"><span data-stu-id="51c5f-195">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="51c5f-196">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="51c5f-196">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="51c5f-197">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="51c5f-197">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a><span data-ttu-id="51c5f-198">用 Safari 测试</span><span class="sxs-lookup"><span data-stu-id="51c5f-198">Test with Safari</span></span>

<span data-ttu-id="51c5f-199">Safari 12 严格实现了之前的草稿，在新的 `None` 值在 cookie 中时失败。</span><span class="sxs-lookup"><span data-stu-id="51c5f-199">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="51c5f-200">通过本文档中[支持旧版浏览](#sob)器的浏览器检测代码，可避免 `None`。</span><span class="sxs-lookup"><span data-stu-id="51c5f-200">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="51c5f-201">使用 MSAL、ADAL 或所使用的任何库，测试 Safari 12、Safari 13 和基于 WebKit 的 OS 样式登录。</span><span class="sxs-lookup"><span data-stu-id="51c5f-201">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="51c5f-202">此问题依赖于基础操作系统版本。</span><span class="sxs-lookup"><span data-stu-id="51c5f-202">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="51c5f-203">已知 OSX Mojave （10.14）和 iOS 12 与新的 SameSite 行为存在兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="51c5f-203">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="51c5f-204">将 OS 升级到 OSX Catalina （10.15）或 iOS 13 会解决此问题。</span><span class="sxs-lookup"><span data-stu-id="51c5f-204">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="51c5f-205">Safari 当前没有用于测试新规范行为的选择标记。</span><span class="sxs-lookup"><span data-stu-id="51c5f-205">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="51c5f-206">用 Firefox 测试</span><span class="sxs-lookup"><span data-stu-id="51c5f-206">Test with Firefox</span></span>

<span data-ttu-id="51c5f-207">可以在版本 68 + 上测试对新标准的 Firefox 支持，方法是在 "`about:config`" 页面上选择 "`network.cookie.sameSite.laxByDefault`的功能标志。</span><span class="sxs-lookup"><span data-stu-id="51c5f-207">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="51c5f-208">以前版本的 Firefox 没有出现兼容性问题的报告。</span><span class="sxs-lookup"><span data-stu-id="51c5f-208">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="51c5f-209">通过 Edge 浏览器进行测试</span><span class="sxs-lookup"><span data-stu-id="51c5f-209">Test with Edge browser</span></span>

<span data-ttu-id="51c5f-210">Edge 支持旧的 SameSite 标准。</span><span class="sxs-lookup"><span data-stu-id="51c5f-210">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="51c5f-211">边缘版本44与新的标准没有任何已知的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="51c5f-211">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="51c5f-212">带边缘测试（Chromium）</span><span class="sxs-lookup"><span data-stu-id="51c5f-212">Test with Edge (Chromium)</span></span>

<span data-ttu-id="51c5f-213">在 `edge://flags/#same-site-by-default-cookies` 页上设置 SameSite 标志。</span><span class="sxs-lookup"><span data-stu-id="51c5f-213">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="51c5f-214">未发现边缘 Chromium 的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="51c5f-214">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="51c5f-215">用 Electron 进行测试</span><span class="sxs-lookup"><span data-stu-id="51c5f-215">Test with Electron</span></span>

<span data-ttu-id="51c5f-216">Electron 的版本包括较早版本的 Chromium。</span><span class="sxs-lookup"><span data-stu-id="51c5f-216">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="51c5f-217">例如，团队使用的 Electron 版本为 Chromium 66，该版本展示了较旧的行为。</span><span class="sxs-lookup"><span data-stu-id="51c5f-217">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="51c5f-218">你必须使用你的产品使用的 Electron 版本执行你自己的兼容性测试。</span><span class="sxs-lookup"><span data-stu-id="51c5f-218">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="51c5f-219">请参阅下一节中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="51c5f-219">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51c5f-220">其他资源</span><span class="sxs-lookup"><span data-stu-id="51c5f-220">Additional resources</span></span>

* [<span data-ttu-id="51c5f-221">Chromium 博客：开发人员：准备好新 SameSite = 无;安全 Cookie 设置</span><span class="sxs-lookup"><span data-stu-id="51c5f-221">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="51c5f-222">SameSite cookie 说明</span><span class="sxs-lookup"><span data-stu-id="51c5f-222">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
