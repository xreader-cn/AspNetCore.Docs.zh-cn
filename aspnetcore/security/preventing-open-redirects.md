---
title: 阻止 ASP.NET Core 中的开放重定向攻击
author: ardalis
description: 演示如何防止针对 ASP.NET Core 应用的开放重定向攻击
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: eb18c599d84fd08ffe97867b67a837303af188db
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408144"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="cc306-103">阻止 ASP.NET Core 中的开放重定向攻击</span><span class="sxs-lookup"><span data-stu-id="cc306-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="cc306-104">重定向到通过请求（如 querystring 或窗体数据）指定的 URL 的 web 应用可能会被篡改，以将用户重定向到外部恶意 URL。</span><span class="sxs-lookup"><span data-stu-id="cc306-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="cc306-105">这种篡改称为 "开放重定向攻击"。</span><span class="sxs-lookup"><span data-stu-id="cc306-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="cc306-106">每当应用程序逻辑重定向到指定的 URL 时，必须验证重定向 URL 是否未被篡改。</span><span class="sxs-lookup"><span data-stu-id="cc306-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="cc306-107">ASP.NET Core 提供内置功能，可帮助保护应用免受开放重定向（也称为开放重定向）的攻击。</span><span class="sxs-lookup"><span data-stu-id="cc306-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="cc306-108">什么是开放重定向攻击？</span><span class="sxs-lookup"><span data-stu-id="cc306-108">What is an open redirect attack?</span></span>

<span data-ttu-id="cc306-109">Web 应用程序在访问需要身份验证的资源时经常将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="cc306-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="cc306-110">重定向通常包括 `returnUrl` 查询字符串参数，以便用户在成功登录后可以返回到最初请求的 URL。</span><span class="sxs-lookup"><span data-stu-id="cc306-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="cc306-111">用户进行身份验证后，会重定向到最初请求的 URL。</span><span class="sxs-lookup"><span data-stu-id="cc306-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="cc306-112">由于目标 URL 是在请求的查询字符串中指定的，因此恶意用户可能会篡改 querystring。</span><span class="sxs-lookup"><span data-stu-id="cc306-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="cc306-113">篡改的 querystring 可能允许站点将用户重定向到外部的恶意站点。</span><span class="sxs-lookup"><span data-stu-id="cc306-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="cc306-114">此方法称为 "重定向" （或 "重定向"）攻击。</span><span class="sxs-lookup"><span data-stu-id="cc306-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="cc306-115">示例攻击</span><span class="sxs-lookup"><span data-stu-id="cc306-115">An example attack</span></span>

<span data-ttu-id="cc306-116">恶意用户可能会受到攻击，目的是允许恶意用户访问用户的凭据或敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cc306-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="cc306-117">若要开始攻击，恶意用户结论用户单击指向站点登录页的链接，并将 `returnUrl` 查询字符串值添加到 URL。</span><span class="sxs-lookup"><span data-stu-id="cc306-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="cc306-118">例如，请考虑一个在上 `contoso.com` 包含登录页的应用 `http://contoso.com/Account/LogOn?returnUrl=/Home/About` 。</span><span class="sxs-lookup"><span data-stu-id="cc306-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="cc306-119">攻击执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="cc306-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="cc306-120">用户单击指向的恶意链接 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` （第二个 URL 为 "contoso**1**.com"，而不是 "contoso.com"）。</span><span class="sxs-lookup"><span data-stu-id="cc306-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="cc306-121">用户已成功登录。</span><span class="sxs-lookup"><span data-stu-id="cc306-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="cc306-122">用户被重定向（由站点）到 `http://contoso1.com/Account/LogOn` （看起来与真实站点完全相同的恶意网站）。</span><span class="sxs-lookup"><span data-stu-id="cc306-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="cc306-123">用户重新登录（向恶意网站提供凭据），并被重定向回真实站点。</span><span class="sxs-lookup"><span data-stu-id="cc306-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="cc306-124">用户可能认为他们第一次尝试登录失败，第二次尝试成功。</span><span class="sxs-lookup"><span data-stu-id="cc306-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="cc306-125">用户很可能仍不知道他们的凭据已泄露。</span><span class="sxs-lookup"><span data-stu-id="cc306-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![打开重定向攻击过程](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="cc306-127">除了登录页以外，某些站点还提供重定向页面或终结点。</span><span class="sxs-lookup"><span data-stu-id="cc306-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="cc306-128">假设你的应用程序有一个页面，其中包含一个打开的重定向 `/Home/Redirect` 。</span><span class="sxs-lookup"><span data-stu-id="cc306-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="cc306-129">例如，攻击者可以创建发送到的电子邮件中的链接 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` 。</span><span class="sxs-lookup"><span data-stu-id="cc306-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="cc306-130">典型用户将查看 URL，并将其以你的站点名称开头。</span><span class="sxs-lookup"><span data-stu-id="cc306-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="cc306-131">信任这一点，他们将单击该链接。</span><span class="sxs-lookup"><span data-stu-id="cc306-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="cc306-132">然后，打开的重定向会将用户发送到仿冒网站，该网站看上去与你的站点相同，并且用户可能会登录到他们认为是你的网站。</span><span class="sxs-lookup"><span data-stu-id="cc306-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="cc306-133">防范开放重定向攻击</span><span class="sxs-lookup"><span data-stu-id="cc306-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="cc306-134">开发 web 应用程序时，将所有用户提供的数据视为不可信。</span><span class="sxs-lookup"><span data-stu-id="cc306-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="cc306-135">如果你的应用程序具有基于 URL 内容重定向用户的功能，请确保此类重定向仅在你的应用程序中本地执行（或对已知 URL 执行，而不是在查询字符串中提供的任何 URL）。</span><span class="sxs-lookup"><span data-stu-id="cc306-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="cc306-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="cc306-136">LocalRedirect</span></span>

<span data-ttu-id="cc306-137">使用 `LocalRedirect` 基类中的 helper 方法 `Controller` ：</span><span class="sxs-lookup"><span data-stu-id="cc306-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="cc306-138">`LocalRedirect`如果指定了一个非本地 URL，则将引发异常。</span><span class="sxs-lookup"><span data-stu-id="cc306-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="cc306-139">否则，它的行为与 `Redirect` 方法相同。</span><span class="sxs-lookup"><span data-stu-id="cc306-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="cc306-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="cc306-140">IsLocalUrl</span></span>

<span data-ttu-id="cc306-141">重定向之前，请使用[IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)方法测试 url：</span><span class="sxs-lookup"><span data-stu-id="cc306-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="cc306-142">下面的示例演示如何在重定向前检查 URL 是否是本地的。</span><span class="sxs-lookup"><span data-stu-id="cc306-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="cc306-143">此 `IsLocalUrl` 方法可防止用户无意中重定向到恶意网站。</span><span class="sxs-lookup"><span data-stu-id="cc306-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="cc306-144">您可以记录在您需要本地 URL 的情况下提供非本地 URL 时提供的 URL 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="cc306-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="cc306-145">记录重定向 Url 有助于诊断重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="cc306-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
