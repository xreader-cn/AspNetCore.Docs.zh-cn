---
title: 防止在 ASP.NET Core 中的打开重定向攻击
author: ardalis
description: 演示如何阻止对 ASP.NET Core 应用打开重定向攻击
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815489"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="fa9be-103">防止在 ASP.NET Core 中的打开重定向攻击</span><span class="sxs-lookup"><span data-stu-id="fa9be-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="fa9be-104">将重定向到的 URL，例如查询字符串或窗体数据的请求通过指定的 web 应用可以有可能被篡改，同时将用户重定向到恶意的外部 URL。</span><span class="sxs-lookup"><span data-stu-id="fa9be-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="fa9be-105">此篡改称为开放重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="fa9be-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="fa9be-106">每当应用程序逻辑将重定向到指定的 URL，必须验证重定向 URL 尚未被篡改。</span><span class="sxs-lookup"><span data-stu-id="fa9be-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="fa9be-107">ASP.NET Core 具有内置功能来防止应用打开重定向 （也称为打开重定向） 攻击。</span><span class="sxs-lookup"><span data-stu-id="fa9be-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="fa9be-108">打开重定向攻击是什么？</span><span class="sxs-lookup"><span data-stu-id="fa9be-108">What is an open redirect attack?</span></span>

<span data-ttu-id="fa9be-109">Web 应用程序频繁地将用户重定向到登录页时他们访问要求进行身份验证的资源。</span><span class="sxs-lookup"><span data-stu-id="fa9be-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="fa9be-110">重定向通常包括`returnUrl`查询字符串参数，以便用户可以返回到最初请求的 URL 后它们已成功登录。</span><span class="sxs-lookup"><span data-stu-id="fa9be-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="fa9be-111">对用户进行身份验证后，它们重定向到他们具有最初请求的 URL。</span><span class="sxs-lookup"><span data-stu-id="fa9be-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="fa9be-112">因为请求的查询字符串中指定的目标 URL，则恶意用户可能篡改在查询字符串。</span><span class="sxs-lookup"><span data-stu-id="fa9be-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="fa9be-113">被篡改的查询字符串可能允许将用户重定向到外部、 恶意站点的站点。</span><span class="sxs-lookup"><span data-stu-id="fa9be-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="fa9be-114">这一技术称为开放重定向 （或重定向） 攻击。</span><span class="sxs-lookup"><span data-stu-id="fa9be-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="fa9be-115">示例攻击</span><span class="sxs-lookup"><span data-stu-id="fa9be-115">An example attack</span></span>

<span data-ttu-id="fa9be-116">恶意用户可以开发目的是允许对用户的凭据和敏感信息的恶意用户访问的攻击。</span><span class="sxs-lookup"><span data-stu-id="fa9be-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="fa9be-117">若要开始攻击，恶意用户使用户能够单击指向你网站的登录页面的`returnUrl`添加到的 URL 查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="fa9be-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="fa9be-118">例如，假设应用程序在`contoso.com`包括在登录页`http://contoso.com/Account/LogOn?returnUrl=/Home/About`。</span><span class="sxs-lookup"><span data-stu-id="fa9be-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="fa9be-119">攻击遵循以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fa9be-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="fa9be-120">用户单击的恶意链接`http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn`(第二个 URL 为"contoso**1**.com"，而非"contoso.com")。</span><span class="sxs-lookup"><span data-stu-id="fa9be-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="fa9be-121">在用户成功登录。</span><span class="sxs-lookup"><span data-stu-id="fa9be-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="fa9be-122">用户 （按站点） 重定向到`http://contoso1.com/Account/LogOn`（看上去确实像真正的站点的恶意网站）。</span><span class="sxs-lookup"><span data-stu-id="fa9be-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="fa9be-123">在用户再次登录 （使恶意网站其凭据） 和重定向回真正的站点。</span><span class="sxs-lookup"><span data-stu-id="fa9be-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="fa9be-124">用户可能认为其第一次尝试登录失败，且其第二次尝试成功。</span><span class="sxs-lookup"><span data-stu-id="fa9be-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="fa9be-125">用户很可能仍不知道其凭据被透露。</span><span class="sxs-lookup"><span data-stu-id="fa9be-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![打开重定向攻击过程](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="fa9be-127">除了登录页的某些站点提供重定向页或终结点。</span><span class="sxs-lookup"><span data-stu-id="fa9be-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="fa9be-128">假设您的应用程序的页面包含打开的重定向， `/Home/Redirect`。</span><span class="sxs-lookup"><span data-stu-id="fa9be-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="fa9be-129">攻击者可以创建，例如，将转到一封电子邮件中的链接`[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`。</span><span class="sxs-lookup"><span data-stu-id="fa9be-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="fa9be-130">典型的用户将查看该 URL，请参阅它开始于你的站点名称。</span><span class="sxs-lookup"><span data-stu-id="fa9be-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="fa9be-131">信任的他们会单击此链接。</span><span class="sxs-lookup"><span data-stu-id="fa9be-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="fa9be-132">打开重定向然后会将用户发送到网页仿冒站点，这看起来与你相同，并且用户很可能会登录到他们认为是你的站点。</span><span class="sxs-lookup"><span data-stu-id="fa9be-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="fa9be-133">打开重定向攻击保护</span><span class="sxs-lookup"><span data-stu-id="fa9be-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="fa9be-134">当开发 web 应用程序，将所有用户提供的数据视为不受信任。</span><span class="sxs-lookup"><span data-stu-id="fa9be-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="fa9be-135">如果你的应用程序具有基于 URL 的内容将用户重定向的功能，请确保，这种重定向仅这样的是本地应用程序中 （或已知的 URL，没有任何可能会在查询字符串中提供的 URL）。</span><span class="sxs-lookup"><span data-stu-id="fa9be-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="fa9be-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="fa9be-136">LocalRedirect</span></span>

<span data-ttu-id="fa9be-137">使用`LocalRedirect`帮助器方法基`Controller`类：</span><span class="sxs-lookup"><span data-stu-id="fa9be-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="fa9be-138">`LocalRedirect` 如果指定了非本地 URL，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="fa9be-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="fa9be-139">否则，它的行为就像`Redirect`方法。</span><span class="sxs-lookup"><span data-stu-id="fa9be-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="fa9be-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="fa9be-140">IsLocalUrl</span></span>

<span data-ttu-id="fa9be-141">使用[IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)方法来测试之前将重定向 Url:</span><span class="sxs-lookup"><span data-stu-id="fa9be-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="fa9be-142">下面的示例演示如何检查 URL 重定向之前是否是本地。</span><span class="sxs-lookup"><span data-stu-id="fa9be-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="fa9be-143">`IsLocalUrl`方法可防止用户无意中重定向到恶意站点。</span><span class="sxs-lookup"><span data-stu-id="fa9be-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="fa9be-144">你可以记录非本地 URL 提供在本地 URL 的预期的情况下时提供的 URL 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fa9be-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="fa9be-145">日志记录重定向 Url 可用于诊断重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="fa9be-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
