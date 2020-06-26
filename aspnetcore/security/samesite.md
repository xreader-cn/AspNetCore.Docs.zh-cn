---
title: 在 ASP.NET Core 中使用 SameSite cookie
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用 SameSite cookie
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 68766591ec86e12e5602d741de74e20aec67cf49
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399499"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="8a45c-103">在 ASP.NET Core 中使用 SameSite cookie</span><span class="sxs-lookup"><span data-stu-id="8a45c-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="8a45c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a45c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a45c-105">SameSite 是一种[IETF](https://ietf.org/about/)草案标准，旨在提供一些针对跨站点请求伪造（CSRF）攻击的防护。</span><span class="sxs-lookup"><span data-stu-id="8a45c-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="8a45c-106">最初在[2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)中起草草案标准版已在[2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)中更新。</span><span class="sxs-lookup"><span data-stu-id="8a45c-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="8a45c-107">更新的标准与以前的标准不是向后兼容，以下是最明显的区别：</span><span class="sxs-lookup"><span data-stu-id="8a45c-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="8a45c-108">默认情况下，不带 SameSite 标头的 cookie 被视为 `SameSite=Lax` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="8a45c-109">`SameSite=None`必须使用以允许跨站点 cookie。</span><span class="sxs-lookup"><span data-stu-id="8a45c-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="8a45c-110">断言 `SameSite=None` 还必须标记为的 cookie `Secure` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="8a45c-111">使用的应用程序 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) 可能会遇到 `sameSite=Lax` 或 cookie 的问题， `sameSite=Strict` 因为 `<iframe>` 被视为跨站点方案。</span><span class="sxs-lookup"><span data-stu-id="8a45c-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="8a45c-112">`SameSite=None` [2016 标准](https://tools.ietf.org/html/draft-west-first-party-cookies-07)不允许该值，并导致某些实现将此类 cookie 视为 `SameSite=Strict` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="8a45c-113">请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="8a45c-114">此 `SameSite=Lax` 设置适用于大多数应用程序 cookie。</span><span class="sxs-lookup"><span data-stu-id="8a45c-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="8a45c-115">某些形式的身份验证，例如[OpenID connect](https://openid.net/connect/) （OIDC）和[ws-federation](https://auth0.com/docs/protocols/ws-fed)默认为基于 POST 的重定向。</span><span class="sxs-lookup"><span data-stu-id="8a45c-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="8a45c-116">基于后期的重定向会触发 SameSite 浏览器保护，因此，对这些组件禁用了 SameSite。</span><span class="sxs-lookup"><span data-stu-id="8a45c-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="8a45c-117">由于请求的流动方式不同，大多数[OAuth](https://oauth.net/)登录名不受影响。</span><span class="sxs-lookup"><span data-stu-id="8a45c-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="8a45c-118">发出 cookie 的每个 ASP.NET Core 组件都需要确定 SameSite 是否合适。</span><span class="sxs-lookup"><span data-stu-id="8a45c-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="8a45c-119">SameSite 测试示例代码</span><span class="sxs-lookup"><span data-stu-id="8a45c-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="8a45c-120">可下载和测试以下示例：</span><span class="sxs-lookup"><span data-stu-id="8a45c-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="8a45c-121">示例</span><span class="sxs-lookup"><span data-stu-id="8a45c-121">Sample</span></span>               | <span data-ttu-id="8a45c-122">文档</span><span class="sxs-lookup"><span data-stu-id="8a45c-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="8a45c-123">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8a45c-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="8a45c-124">[.NET Core Razor 页](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="8a45c-124">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a45c-125">可下载并测试以下示例：</span><span class="sxs-lookup"><span data-stu-id="8a45c-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="8a45c-126">示例</span><span class="sxs-lookup"><span data-stu-id="8a45c-126">Sample</span></span>               | <span data-ttu-id="8a45c-127">文档</span><span class="sxs-lookup"><span data-stu-id="8a45c-127">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="8a45c-128">[.NET Core Razor 页](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="8a45c-128">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="8a45c-129">.NET Core 对 sameSite 属性的支持</span><span class="sxs-lookup"><span data-stu-id="8a45c-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="8a45c-130">由于2.2 年12月2019更新发布，.NET Core 支持 SameSite 的2019草案标准。</span><span class="sxs-lookup"><span data-stu-id="8a45c-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="8a45c-131">开发人员能够使用属性以编程方式控制 sameSite 属性的值 `HttpCookie.SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="8a45c-132">将 `SameSite` 属性设置为 Strict、宽松或 None 会导致这些值用 cookie 写入网络。</span><span class="sxs-lookup"><span data-stu-id="8a45c-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="8a45c-133">如果将其设置为（SameSiteMode）（-1），则表示网络上不应包含 cookie 为的 sameSite 属性</span><span class="sxs-lookup"><span data-stu-id="8a45c-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="8a45c-134">.NET Core 3.0 支持更新后的 SameSite 值，并向枚举添加额外的枚举值 `SameSiteMode.Unspecified` `SameSiteMode` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="8a45c-135">此新值指示不应向 cookie 发送任何 sameSite。</span><span class="sxs-lookup"><span data-stu-id="8a45c-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="8a45c-136">12月修补程序行为更改</span><span class="sxs-lookup"><span data-stu-id="8a45c-136">December patch behavior changes</span></span>

<span data-ttu-id="8a45c-137">.NET Framework 和 .NET Core 2.1 的特定行为更改是 `SameSite` 属性解释值的方式 `None` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="8a45c-138">在修补程序的值为 `None` "根本不发出属性" 之前，修补后表示 "发出值为的属性" `None` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="8a45c-139">修补后，的 `SameSite` 值将 `(SameSiteMode)(-1)` 导致不发出属性。</span><span class="sxs-lookup"><span data-stu-id="8a45c-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="8a45c-140">Forms 身份验证和会话状态 cookie 的默认 SameSite 值从更改 `None` 为 `Lax` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="8a45c-141">使用 SameSite 的 API 用法</span><span class="sxs-lookup"><span data-stu-id="8a45c-141">API usage with SameSite</span></span>

<span data-ttu-id="8a45c-142">[Httpcontext.current](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)默认值为 `Unspecified` ，这意味着不会向 Cookie 中添加 SameSite 属性，客户端将使用其默认行为（对于新浏览器是不严格的，对于旧浏览器则为 "无"）。</span><span class="sxs-lookup"><span data-stu-id="8a45c-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="8a45c-143">下面的代码演示如何将 cookie SameSite 值更改为 `SameSiteMode.Lax` ：</span><span class="sxs-lookup"><span data-stu-id="8a45c-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8a45c-144">发出 cookie 的所有 ASP.NET Core 组件都用适用于其方案的设置替代前面的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a45c-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="8a45c-145">先前重写的默认值尚未更改。</span><span class="sxs-lookup"><span data-stu-id="8a45c-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="8a45c-146">组件</span><span class="sxs-lookup"><span data-stu-id="8a45c-146">Component</span></span> | <span data-ttu-id="8a45c-147">票证</span><span class="sxs-lookup"><span data-stu-id="8a45c-147">cookie</span></span> | <span data-ttu-id="8a45c-148">默认</span><span class="sxs-lookup"><span data-stu-id="8a45c-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="8a45c-149">SessionOptions</span><span class="sxs-lookup"><span data-stu-id="8a45c-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="8a45c-150">CookieTempDataProviderOptions</span><span class="sxs-lookup"><span data-stu-id="8a45c-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="8a45c-151">AntiforgeryOptions</span><span class="sxs-lookup"><span data-stu-id="8a45c-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="8a45c-152">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="8a45c-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="8a45c-153">Cookieauthenticationoptions.authenticationtype</span><span class="sxs-lookup"><span data-stu-id="8a45c-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="8a45c-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="8a45c-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="8a45c-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="8a45c-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="8a45c-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="8a45c-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="8a45c-157">Httpcontext.current。追加</span><span class="sxs-lookup"><span data-stu-id="8a45c-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="8a45c-158">ASP.NET Core 3.1 和更高版本提供了以下 SameSite 支持：</span><span class="sxs-lookup"><span data-stu-id="8a45c-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="8a45c-159">重新定义要发出的的行为 `SameSiteMode.None``SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="8a45c-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="8a45c-160">添加新值 `SameSiteMode.Unspecified` 以省略 SameSite 属性。</span><span class="sxs-lookup"><span data-stu-id="8a45c-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="8a45c-161">所有 cookie Api 默认为 `Unspecified` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="8a45c-162">某些使用 cookie 的组件会将值设置得更加特定于其应用场景。</span><span class="sxs-lookup"><span data-stu-id="8a45c-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="8a45c-163">有关示例，请参阅上表。</span><span class="sxs-lookup"><span data-stu-id="8a45c-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a45c-164">在 ASP.NET Core 3.0 及更高版本中，SameSite 默认值已更改，以避免与客户端默认值不一致发生冲突。</span><span class="sxs-lookup"><span data-stu-id="8a45c-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="8a45c-165">以下 Api 已将默认值从更改 `SameSiteMode.Lax ` 为 `-1` ，以避免发出这些 Cookie 的 SameSite 属性：</span><span class="sxs-lookup"><span data-stu-id="8a45c-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="8a45c-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>与[HttpContext](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)一起使用</span><span class="sxs-lookup"><span data-stu-id="8a45c-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="8a45c-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>用作的工厂`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="8a45c-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="8a45c-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="8a45c-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="8a45c-169">历史记录和更改</span><span class="sxs-lookup"><span data-stu-id="8a45c-169">History and changes</span></span>

<span data-ttu-id="8a45c-170">SameSite 支持在2.0 中第一次 ASP.NET Core 实现，使用[2016 草案标准](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="8a45c-171">2016标准已选择加入。</span><span class="sxs-lookup"><span data-stu-id="8a45c-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="8a45c-172">默认情况下，ASP.NET Core 选择加入几个 cookie `Lax` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="8a45c-173">在遇到身份验证的几个[问题](https://github.com/aspnet/Announcements/issues/318)后，大多数 SameSite 使用已[禁用](https://github.com/aspnet/Announcements/issues/348)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="8a45c-174">2019年11月发布了[修补程序](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)，从2016标准版更新为2019标准。</span><span class="sxs-lookup"><span data-stu-id="8a45c-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="8a45c-175">[SameSite 规范的2019草案](https://github.com/aspnet/Announcements/issues/390)：</span><span class="sxs-lookup"><span data-stu-id="8a45c-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="8a45c-176">**不**向后兼容2016草案。</span><span class="sxs-lookup"><span data-stu-id="8a45c-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="8a45c-177">有关详细信息，请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="8a45c-178">指定 `SameSite=Lax` 按默认值处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="8a45c-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="8a45c-179">指定显式断言以便 `SameSite=None` 启用跨站点传递的 cookie 应标记为 `Secure` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="8a45c-180">`None`要选择退出的新项。</span><span class="sxs-lookup"><span data-stu-id="8a45c-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="8a45c-181">为 ASP.NET Core 2.1、2.2 和3.0 颁发的修补程序支持。</span><span class="sxs-lookup"><span data-stu-id="8a45c-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="8a45c-182">ASP.NET Core 3.1 具有附加的 SameSite 支持。</span><span class="sxs-lookup"><span data-stu-id="8a45c-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="8a45c-183">默认[情况下，计划](https://chromestatus.com/feature/5088147346030592)在[2020 年2月](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)启用。</span><span class="sxs-lookup"><span data-stu-id="8a45c-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="8a45c-184">浏览器已开始在2019中移动到此标准。</span><span class="sxs-lookup"><span data-stu-id="8a45c-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="8a45c-185">受从 2016 SameSite 草案标准到2019草案标准的更改影响的 Api</span><span class="sxs-lookup"><span data-stu-id="8a45c-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="8a45c-186">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="8a45c-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="8a45c-187">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="8a45c-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="8a45c-188">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="8a45c-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="8a45c-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="8a45c-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="8a45c-190">支持旧版浏览器</span><span class="sxs-lookup"><span data-stu-id="8a45c-190">Supporting older browsers</span></span>

<span data-ttu-id="8a45c-191">2016 SameSite 标准规定，未知值必须被视为 `SameSite=Strict` 值。</span><span class="sxs-lookup"><span data-stu-id="8a45c-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="8a45c-192">从支持 2016 SameSite 标准的旧版浏览器访问的应用在收到值为的 SameSite 属性时可能会中断 `None` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="8a45c-193">如果 Web 应用要支持较旧的浏览器，则必须实现浏览器检测。</span><span class="sxs-lookup"><span data-stu-id="8a45c-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="8a45c-194">ASP.NET Core 不会实现浏览器检测，因为用户代理值非常稳定且频繁更改。</span><span class="sxs-lookup"><span data-stu-id="8a45c-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="8a45c-195">中的扩展点 <xref:Microsoft.AspNetCore.CookiePolicy> 允许插入特定于用户代理的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8a45c-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="8a45c-196">在中 `Startup.Configure` ，添加在调用之前调用的代码 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或*任何*写入 cookie 的方法：</span><span class="sxs-lookup"><span data-stu-id="8a45c-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="8a45c-197">在中 `Startup.ConfigureServices` ，添加类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="8a45c-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="8a45c-198">在前面的示例中， `MyUserAgentDetectionLib.DisallowsSameSiteNone` 是一个用户提供的库，用于检测用户代理是否不支持 SameSite `None` ：</span><span class="sxs-lookup"><span data-stu-id="8a45c-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="8a45c-199">下面的代码演示了一个示例 `DisallowsSameSiteNone` 方法：</span><span class="sxs-lookup"><span data-stu-id="8a45c-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="8a45c-200">以下代码仅用于演示：</span><span class="sxs-lookup"><span data-stu-id="8a45c-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="8a45c-201">不应将其视为已完成。</span><span class="sxs-lookup"><span data-stu-id="8a45c-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="8a45c-202">它不维护或不受支持。</span><span class="sxs-lookup"><span data-stu-id="8a45c-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="8a45c-203">测试应用的 SameSite 问题</span><span class="sxs-lookup"><span data-stu-id="8a45c-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="8a45c-204">与远程站点（如通过第三方登录）交互的应用需要：</span><span class="sxs-lookup"><span data-stu-id="8a45c-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="8a45c-205">测试多个浏览器上的交互。</span><span class="sxs-lookup"><span data-stu-id="8a45c-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="8a45c-206">应用本文档中讨论的[CookiePolicy 浏览器检测和缓解措施](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="8a45c-207">使用可选择新的 SameSite 行为的客户端版本测试 web 应用。</span><span class="sxs-lookup"><span data-stu-id="8a45c-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="8a45c-208">Chrome、Firefox 和 Chromium Edge 都具有可用于测试的新的可选功能标志。</span><span class="sxs-lookup"><span data-stu-id="8a45c-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="8a45c-209">应用应用 SameSite 修补程序后，请对其进行测试，使其与早期版本的客户端版本（尤其是 Safari）</span><span class="sxs-lookup"><span data-stu-id="8a45c-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="8a45c-210">有关详细信息，请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="8a45c-211">用 Chrome 测试</span><span class="sxs-lookup"><span data-stu-id="8a45c-211">Test with Chrome</span></span>

<span data-ttu-id="8a45c-212">Chrome 78 + 提供了令人误解的结果，因为它具有临时的缓解措施。</span><span class="sxs-lookup"><span data-stu-id="8a45c-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="8a45c-213">Chrome 78 + 暂时缓解功能允许 cookie 不到两分钟。</span><span class="sxs-lookup"><span data-stu-id="8a45c-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="8a45c-214">已启用适当测试标志的 Chrome 76 或77提供更准确的结果。</span><span class="sxs-lookup"><span data-stu-id="8a45c-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="8a45c-215">若要测试新的 SameSite 行为切换 `chrome://flags/#same-site-by-default-cookies` 为**启用状态**。</span><span class="sxs-lookup"><span data-stu-id="8a45c-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="8a45c-216">旧版本的 Chrome （75及更低版本）将报告为失败，并出现新 `None` 设置。</span><span class="sxs-lookup"><span data-stu-id="8a45c-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="8a45c-217">请参阅本文档中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="8a45c-218">Google 不会使旧版 chrome 版本可用。</span><span class="sxs-lookup"><span data-stu-id="8a45c-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="8a45c-219">遵循[下载 Chromium](https://www.chromium.org/getting-involved/download-chromium)中的说明来测试旧版 Chrome。</span><span class="sxs-lookup"><span data-stu-id="8a45c-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="8a45c-220">不要从通过搜索旧版 chrome 提供的**链接下载 chrome** 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="8a45c-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="8a45c-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="8a45c-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="8a45c-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="8a45c-223">从 "未完成" 的版本开始 `80.0.3975.0` ，可以使用新标志来禁用宽松的 + 后续暂时缓解，以 `--enable-features=SameSiteDefaultChecksMethodRigorously` 允许在删除缓解功能的最终状态下测试站点和服务。</span><span class="sxs-lookup"><span data-stu-id="8a45c-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="8a45c-224">有关详细信息，请参阅 Chromium 项目[SameSite Updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="8a45c-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="8a45c-225">用 Safari 测试</span><span class="sxs-lookup"><span data-stu-id="8a45c-225">Test with Safari</span></span>

<span data-ttu-id="8a45c-226">Safari 12 严格实现了之前的草稿，在新 `None` 值在 cookie 中时失败。</span><span class="sxs-lookup"><span data-stu-id="8a45c-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="8a45c-227">`None`通过本文档中[支持旧版浏览](#sob)器的浏览器检测代码，避免了这种情况。</span><span class="sxs-lookup"><span data-stu-id="8a45c-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="8a45c-228">使用 MSAL、ADAL 或所使用的任何库，测试 Safari 12、Safari 13 和基于 WebKit 的 OS 样式登录。</span><span class="sxs-lookup"><span data-stu-id="8a45c-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="8a45c-229">问题取决于基础 OS 版本。</span><span class="sxs-lookup"><span data-stu-id="8a45c-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="8a45c-230">已知 OSX Mojave （10.14）和 iOS 12 与新的 SameSite 行为存在兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="8a45c-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="8a45c-231">将 OS 升级到 OSX Catalina （10.15）或 iOS 13 会解决此问题。</span><span class="sxs-lookup"><span data-stu-id="8a45c-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="8a45c-232">Safari 当前没有用于测试新规范行为的选择标记。</span><span class="sxs-lookup"><span data-stu-id="8a45c-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="8a45c-233">用 Firefox 测试</span><span class="sxs-lookup"><span data-stu-id="8a45c-233">Test with Firefox</span></span>

<span data-ttu-id="8a45c-234">可以在版本 68 + 上测试对新标准的 Firefox 支持，方法是在页面上选择 `about:config` 功能标志 `network.cookie.sameSite.laxByDefault` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="8a45c-235">以前版本的 Firefox 没有出现兼容性问题的报告。</span><span class="sxs-lookup"><span data-stu-id="8a45c-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="8a45c-236">通过 Edge 浏览器进行测试</span><span class="sxs-lookup"><span data-stu-id="8a45c-236">Test with Edge browser</span></span>

<span data-ttu-id="8a45c-237">Edge 支持旧的 SameSite 标准。</span><span class="sxs-lookup"><span data-stu-id="8a45c-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="8a45c-238">边缘版本44与新的标准没有任何已知的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="8a45c-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="8a45c-239">带边缘测试（Chromium）</span><span class="sxs-lookup"><span data-stu-id="8a45c-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="8a45c-240">页面上设置了 SameSite 标志 `edge://flags/#same-site-by-default-cookies` 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="8a45c-241">未发现边缘 Chromium 的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="8a45c-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="8a45c-242">测试Electron</span><span class="sxs-lookup"><span data-stu-id="8a45c-242">Test with Electron</span></span>

<span data-ttu-id="8a45c-243">版本的 Electron 包含 Chromium 的旧版本。</span><span class="sxs-lookup"><span data-stu-id="8a45c-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="8a45c-244">例如，团队使用的版本 Electron 是 Chromium 66，它展示了较旧的行为。</span><span class="sxs-lookup"><span data-stu-id="8a45c-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="8a45c-245">你必须使用产品的版本来执行你自己的兼容性测试 Electron 。</span><span class="sxs-lookup"><span data-stu-id="8a45c-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="8a45c-246">请参阅下一节中的[支持旧版浏览器](#sob)。</span><span class="sxs-lookup"><span data-stu-id="8a45c-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a45c-247">其他资源</span><span class="sxs-lookup"><span data-stu-id="8a45c-247">Additional resources</span></span>

* [<span data-ttu-id="8a45c-248">Chromium 博客：开发人员：准备好新 SameSite = 无;安全 Cookie 设置</span><span class="sxs-lookup"><span data-stu-id="8a45c-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="8a45c-249">SameSite cookie 说明</span><span class="sxs-lookup"><span data-stu-id="8a45c-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="8a45c-250">2019年11月修补程序</span><span class="sxs-lookup"><span data-stu-id="8a45c-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="8a45c-251">示例</span><span class="sxs-lookup"><span data-stu-id="8a45c-251">Sample</span></span>               | <span data-ttu-id="8a45c-252">文档</span><span class="sxs-lookup"><span data-stu-id="8a45c-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="8a45c-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8a45c-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="8a45c-254">[.NET Core Razor 页](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="8a45c-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="8a45c-255">示例</span><span class="sxs-lookup"><span data-stu-id="8a45c-255">Sample</span></span>               | <span data-ttu-id="8a45c-256">文档</span><span class="sxs-lookup"><span data-stu-id="8a45c-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="8a45c-257">[.NET Core Razor 页](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="8a45c-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
