---
title: 阻止跨站点请求伪造 (XSRF/CSRF) 攻击 ASP.NET Core
author: steve-smith
description: 了解如何防止恶意网站可能会影响客户端浏览器与应用程序之间的交互的 web 应用程序的攻击。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: 197954965ee57b2a44ad0217d79ba142114e7df6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060841"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="8b6cd-103">阻止跨站点请求伪造 (XSRF/CSRF) 攻击 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b6cd-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="8b6cd-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8b6cd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8b6cd-105">跨站点请求伪造 (也称为 XSRF 或 CSRF) 是对 web 托管应用程序的攻击，恶意 web 应用可能会影响客户端浏览器和信任该浏览器的 web 应用之间的交互。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="8b6cd-106">这些攻击是可能的，因为 web 浏览器会自动向网站发送每个请求的身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="8b6cd-107">这种形式的攻击也称为 *一键式攻击* 或 *会话 riding* ，因为攻击利用用户以前的经过身份验证的会话。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="8b6cd-108">CSRF 攻击的示例：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="8b6cd-109">用户 `www.good-banking-site.com` 使用窗体身份验证登录。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="8b6cd-110">服务器对用户进行身份验证，并发出包含身份验证的响应 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-110">The server authenticates the user and issues a response that includes an authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-111">此站点容易受到攻击，因为它信任使用有效身份验证接收的任何请求 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication :::no-loc(cookie):::.</span></span>
1. <span data-ttu-id="8b6cd-112">用户访问恶意网站 `www.bad-crook-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="8b6cd-113">恶意站点包含如下 `www.bad-crook-site.com` 所示的 HTML 格式：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="8b6cd-114">请注意，表单 `action` 会发布到易受攻击的站点，而不是恶意站点。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="8b6cd-115">这是 CSRF 的 "跨站点" 部分。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="8b6cd-116">用户选择 "提交" 按钮。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-116">The user selects the submit button.</span></span> <span data-ttu-id="8b6cd-117">浏览器发出请求并自动包含请求域的身份验证 :::no-loc(cookie)::: `www.good-banking-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-117">The browser makes the request and automatically includes the authentication :::no-loc(cookie)::: for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="8b6cd-118">请求在 `www.good-banking-site.com` 服务器上使用用户的身份验证上下文运行，并可执行允许用户执行的任何操作。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="8b6cd-119">除了用户选择按钮以提交窗体的情况外，恶意网站还可以：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="8b6cd-120">运行自动提交窗体的脚本。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="8b6cd-121">以 AJAX 请求形式发送窗体提交。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="8b6cd-122">使用 CSS 隐藏窗体。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-122">Hide the form using CSS.</span></span>

<span data-ttu-id="8b6cd-123">除最初访问恶意网站外，这些备选方案不需要用户执行任何操作或输入。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="8b6cd-124">使用 HTTPS 不会阻止 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="8b6cd-125">恶意站点可以发送请求，就像发送不 `https://www.good-banking-site.com/` 安全请求一样容易。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="8b6cd-126">某些攻击目标是响应 GET 请求的终结点，在这种情况下，可以使用图像标记执行操作。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="8b6cd-127">这种形式的攻击在允许图像但阻止 JavaScript 的论坛站点上很常见。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="8b6cd-128">在 GET 请求上更改状态的应用（其中变量或资源被更改）很容易受到恶意攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="8b6cd-129">**更改状态的 GET 请求是不安全的。最佳做法是从不更改 GET 请求的状态。**</span><span class="sxs-lookup"><span data-stu-id="8b6cd-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="8b6cd-130">:::no-loc(cookie):::由于以下原因，对使用 s 进行身份验证的 web 应用可能会受到 CSRF 攻击：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-130">CSRF attacks are possible against web apps that use :::no-loc(cookie):::s for authentication because:</span></span>

* <span data-ttu-id="8b6cd-131">浏览器存储 :::no-loc(cookie)::: 由 web 应用颁发的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-131">Browsers store :::no-loc(cookie):::s issued by a web app.</span></span>
* <span data-ttu-id="8b6cd-132">存储 :::no-loc(cookie)::: :::no-loc(cookie)::: 的包含经过身份验证的用户的会话。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-132">Stored :::no-loc(cookie):::s include session :::no-loc(cookie):::s for authenticated users.</span></span>
* <span data-ttu-id="8b6cd-133">:::no-loc(cookie):::无论在浏览器中生成应用程序请求的方式如何，浏览器都将所有与域关联的都发送到 web 应用。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-133">Browsers send all of the :::no-loc(cookie):::s associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="8b6cd-134">但是，CSRF 攻击并不局限于利用 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-134">However, CSRF attacks aren't limited to exploiting :::no-loc(cookie):::s.</span></span> <span data-ttu-id="8b6cd-135">例如，基本身份验证和摘要式身份验证也容易受到攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="8b6cd-136">用户使用基本或摘要式身份验证登录后，浏览器会自动发送凭据，直到会话 &dagger; 结束。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="8b6cd-137">&dagger;在这种情况下， *会话* 是指在其中对用户进行身份验证的客户端会话。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="8b6cd-138">它与服务器端会话或 [ASP.NET Core 会话中间件](xref:fundamentals/app-state)无关。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="8b6cd-139">用户可以采取预防措施来防止 CSRF 漏洞：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="8b6cd-140">使用完 web 应用后，将其注销。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="8b6cd-141">定期清除浏览器 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-141">Clear browser :::no-loc(cookie):::s periodically.</span></span>

<span data-ttu-id="8b6cd-142">但是，CSRF 漏洞在本质上是 web 应用的问题，而不是最终用户的问题。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="8b6cd-143">身份验证基础知识</span><span class="sxs-lookup"><span data-stu-id="8b6cd-143">Authentication fundamentals</span></span>

<span data-ttu-id="8b6cd-144">:::no-loc(Cookie):::基于的身份验证是一种常用的身份验证形式。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-144">:::no-loc(Cookie):::-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="8b6cd-145">基于令牌的身份验证系统不断增长，尤其是对于单页面应用程序 (Spa) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="8b6cd-146">:::no-loc(Cookie):::基于的身份验证</span><span class="sxs-lookup"><span data-stu-id="8b6cd-146">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="8b6cd-147">用户使用其用户名和密码进行身份验证时，将颁发令牌，其中包含可用于身份验证和授权的身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="8b6cd-148">该令牌存储为 :::no-loc(cookie)::: 客户端发出的每个请求所附带的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-148">The token is stored as a :::no-loc(cookie)::: that accompanies every request the client makes.</span></span> <span data-ttu-id="8b6cd-149">生成和验证 :::no-loc(cookie)::: 是由 :::no-loc(Cookie)::: 身份验证中间件执行的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-149">Generating and validating this :::no-loc(cookie)::: is performed by the :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="8b6cd-150">[中间件](xref:fundamentals/middleware/index)将用户主体序列化为已加密 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-151">在后续请求中，中间件将验证 :::no-loc(cookie)::: 、重新创建主体，并将主体分配给[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)的[用户](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)属性。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-151">On subsequent requests, the middleware validates the :::no-loc(cookie):::, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="8b6cd-152">基于令牌的身份验证</span><span class="sxs-lookup"><span data-stu-id="8b6cd-152">Token-based authentication</span></span>

<span data-ttu-id="8b6cd-153">对用户进行身份验证时，会将令牌颁发 (不是防伪令牌) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="8b6cd-154">令牌包含 [声明](/dotnet/framework/security/claims-based-identity-model) 形式的用户信息或引用令牌，该令牌将应用指向应用中维护的用户状态。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="8b6cd-155">当用户尝试访问要求身份验证的资源时，会将令牌发送到应用程序，并以持有者令牌的形式提供附加的授权标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="8b6cd-156">这使应用无状态。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-156">This makes the app stateless.</span></span> <span data-ttu-id="8b6cd-157">在每个后续请求中，将在请求服务器端验证时传递该令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="8b6cd-158">此标记未 *加密* ; *编码* 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-158">This token isn't *encrypted* ; it's *encoded* .</span></span> <span data-ttu-id="8b6cd-159">在服务器上，将解码令牌来访问其信息。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="8b6cd-160">若要在后续请求中发送令牌，请将该令牌存储在浏览器的本地存储中。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="8b6cd-161">如果令牌存储在浏览器的本地存储中，请不要担心 CSRF 漏洞。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="8b6cd-162">当令牌存储在中时，CSRF 是一个问题 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-162">CSRF is a concern when the token is stored in a :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-163">有关详细信息，请参阅 GitHub 问题 [SPA 代码示例将添加 :::no-loc(cookie)::: 两个](https://github.com/dotnet/AspNetCore.Docs/issues/13369)。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-163">For more information, see the GitHub issue [SPA code sample adds two :::no-loc(cookie):::s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="8b6cd-164">在一个域中托管多个应用</span><span class="sxs-lookup"><span data-stu-id="8b6cd-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="8b6cd-165">共享宿主环境容易遭受会话劫持、登录 CSRF 和其他攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="8b6cd-166">尽管 `example1.contoso.net` 和 `example2.contoso.net` 是不同的主机，但该域下的主机之间存在隐式信任关系 `*.contoso.net` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="8b6cd-167">这种隐式信任关系允许潜在的不受信任的主机影响彼此的 :::no-loc(cookie)::: (相同的源策略，这些策略控制 AJAX 请求并不一定应用于 HTTP :::no-loc(cookie)::: s) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's :::no-loc(cookie):::s (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP :::no-loc(cookie):::s).</span></span>

<span data-ttu-id="8b6cd-168">如果 :::no-loc(cookie)::: 不共享域，则可以阻止利用同一域中托管的应用之间受信任的攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-168">Attacks that exploit trusted :::no-loc(cookie):::s between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="8b6cd-169">当每个应用托管在其自己的域中时，没有 :::no-loc(cookie)::: 要利用的隐式信任关系。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-169">When each app is hosted on its own domain, there is no implicit :::no-loc(cookie)::: trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="8b6cd-170">ASP.NET Core 防伪配置</span><span class="sxs-lookup"><span data-stu-id="8b6cd-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="8b6cd-171">ASP.NET Core 使用 [ASP.NET Core 数据保护](xref:security/data-protection/introduction)来实现防伪。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="8b6cd-172">必须将数据保护堆栈配置为在服务器场中运行。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="8b6cd-173">有关详细信息，请参阅 [配置数据保护](xref:security/data-protection/configuration/overview) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b6cd-174">当在中调用以下某个 Api 时，防伪中间件将添加到 [依赖关系注入](xref:fundamentals/dependency-injection) 容器中 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b6cd-175">当在中调用时，防伪中间件将添加到 [依赖关系注入](xref:fundamentals/dependency-injection) 容器 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 中 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8b6cd-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="8b6cd-176">在 ASP.NET Core 2.0 或更高版本中， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 将防伪标记注入 HTML 窗体元素。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="8b6cd-177">文件中的以下标记 :::no-loc(Razor)::: 会自动生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-177">The following markup in a :::no-loc(Razor)::: file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="8b6cd-178">同样，如果窗体的方法不获取，则默认情况下， [IHtmlHelper](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 将生成防伪标记。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="8b6cd-179">当 `<form>` 标记包含 `method="post"` 属性并且满足以下任一条件时，会自动生成 HTML 窗体元素的防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="8b6cd-180">操作属性为空 (`action=""`) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="8b6cd-181">不 () 提供操作属性 `<form method="post">` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="8b6cd-182">可以禁用 HTML 窗体元素的自动生成防伪标记：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="8b6cd-183">显式禁用属性为的防伪令牌 `asp-antiforgery` ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="8b6cd-184">Form 元素使用 Tag Helper [！ opt out 符号](xref:mvc/views/tag-helpers/intro#opt-out)选择退出标记帮助器：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="8b6cd-185">`FormTagHelper`从视图中删除。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="8b6cd-186">`FormTagHelper`可以通过将以下指令添加到视图中，从视图中删除 :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-186">The `FormTagHelper` can be removed from a view by adding the following directive to the :::no-loc(Razor)::: view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="8b6cd-187">[ :::no-loc(Razor)::: 页面](xref:razor-pages/index)自动从 XSRF/CSRF 中保护。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-187">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="8b6cd-188">有关详细信息，请参阅 [XSRF/CSRF 和 :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf)。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-188">For more information, see [XSRF/CSRF and :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="8b6cd-189">防御 CSRF 攻击的最常见方法是使用) 的同步器 *令牌模式* (STP。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="8b6cd-190">当用户请求包含窗体数据的页面时，将使用 STP：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="8b6cd-191">服务器向客户端发送与当前用户的标识关联的令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="8b6cd-192">客户端将该令牌发送回服务器以进行验证。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="8b6cd-193">如果服务器收到的令牌与经过身份验证的用户的标识不匹配，则会拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="8b6cd-194">该令牌是唯一的且不可预测的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="8b6cd-195">该令牌还可用于确保一系列请求的正确排序 (例如，确保的请求序列为： page 1 > 第2页 > 第3页) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="8b6cd-196">ASP.NET Core MVC 和页面模板中的所有表单都 :::no-loc(Razor)::: 生成防伪标记。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-196">All of the forms in ASP.NET Core MVC and :::no-loc(Razor)::: Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="8b6cd-197">以下对视图示例将生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="8b6cd-198">将防伪标记显式添加到 `<form>` 元素，而不将标记帮助程序与 HTML 帮助程序一起使用 [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="8b6cd-199">在上述每种情况下，ASP.NET Core 添加如下所示的隐藏窗体字段：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="8b6cd-200">ASP.NET Core 包括三个用于处理防伪令牌的 [筛选器](xref:mvc/controllers/filters) ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="8b6cd-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="8b6cd-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="8b6cd-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="8b6cd-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="8b6cd-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="8b6cd-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="8b6cd-204">防伪选项</span><span class="sxs-lookup"><span data-stu-id="8b6cd-204">Antiforgery options</span></span>

<span data-ttu-id="8b6cd-205">自定义 [防伪选项](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set :::no-loc(Cookie)::: properties using :::no-loc(Cookie):::Builder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="8b6cd-206">&dagger;`:::no-loc(Cookie):::`使用[ :::no-loc(Cookie)::: 生成器](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder)类的属性设置防伪属性。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-206">&dagger;Set the antiforgery `:::no-loc(Cookie):::` properties using the properties of the [:::no-loc(Cookie):::Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) class.</span></span>

| <span data-ttu-id="8b6cd-207">选项</span><span class="sxs-lookup"><span data-stu-id="8b6cd-207">Option</span></span> | <span data-ttu-id="8b6cd-208">说明</span><span class="sxs-lookup"><span data-stu-id="8b6cd-208">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="8b6cd-209">确定用于创建防伪的设置 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-209">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| [<span data-ttu-id="8b6cd-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="8b6cd-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="8b6cd-211">防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="8b6cd-212">HeaderName</span><span class="sxs-lookup"><span data-stu-id="8b6cd-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="8b6cd-213">防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="8b6cd-214">如果 `null` 为，则系统仅考虑窗体数据。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="8b6cd-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="8b6cd-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="8b6cd-216">指定是否取消生成 `X-Frame-Options` 标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="8b6cd-217">默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="8b6cd-218">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.:::no-loc(Cookie):::Domain = "contoso.com";
    options.:::no-loc(Cookie):::Name = "X-CSRF-TOKEN-COOKIENAME";
    options.:::no-loc(Cookie):::Path = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="8b6cd-219">选项</span><span class="sxs-lookup"><span data-stu-id="8b6cd-219">Option</span></span> | <span data-ttu-id="8b6cd-220">说明</span><span class="sxs-lookup"><span data-stu-id="8b6cd-220">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="8b6cd-221">确定用于创建防伪的设置 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-221">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| <span data-ttu-id="8b6cd-222">[:::no-loc(Cookie):::域](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span><span class="sxs-lookup"><span data-stu-id="8b6cd-222">[:::no-loc(Cookie):::Domain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span></span> | <span data-ttu-id="8b6cd-223">的域 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-223">The domain of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-224">默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-224">Defaults to `null`.</span></span> <span data-ttu-id="8b6cd-225">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="8b6cd-226">建议的替代项为 :::no-loc(Cookie)::: 。域名.</span><span class="sxs-lookup"><span data-stu-id="8b6cd-226">The recommended alternative is :::no-loc(Cookie):::.Domain.</span></span> |
| <span data-ttu-id="8b6cd-227">[:::no-loc(Cookie):::名称](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span><span class="sxs-lookup"><span data-stu-id="8b6cd-227">[:::no-loc(Cookie):::Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span></span> | <span data-ttu-id="8b6cd-228">:::no-loc(cookie)::: 的名称。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-228">The name of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-229">如果未设置，系统将生成一个以 [默认 :::no-loc(Cookie)::: 前缀](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) ( "开头的唯一名称。AspNetCore. 防伪. ") 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-229">If not set, the system generates a unique name beginning with the [Default:::no-loc(Cookie):::Prefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="8b6cd-230">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="8b6cd-231">建议的替代项为 :::no-loc(Cookie)::: 。路径名.</span><span class="sxs-lookup"><span data-stu-id="8b6cd-231">The recommended alternative is :::no-loc(Cookie):::.Name.</span></span> |
| <span data-ttu-id="8b6cd-232">[:::no-loc(Cookie):::路径](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span><span class="sxs-lookup"><span data-stu-id="8b6cd-232">[:::no-loc(Cookie):::Path](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span></span> | <span data-ttu-id="8b6cd-233">在上设置的路径 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-233">The path set on the :::no-loc(cookie):::.</span></span> <span data-ttu-id="8b6cd-234">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="8b6cd-235">建议的替代项为 :::no-loc(Cookie)::: 。通道.</span><span class="sxs-lookup"><span data-stu-id="8b6cd-235">The recommended alternative is :::no-loc(Cookie):::.Path.</span></span> |
| [<span data-ttu-id="8b6cd-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="8b6cd-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="8b6cd-237">防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="8b6cd-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="8b6cd-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="8b6cd-239">防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="8b6cd-240">如果 `null` 为，则系统仅考虑窗体数据。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="8b6cd-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="8b6cd-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="8b6cd-242">指定防伪系统是否需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="8b6cd-243">如果为 `true` ，则非 HTTPS 请求会失败。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="8b6cd-244">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-244">Defaults to `false`.</span></span> <span data-ttu-id="8b6cd-245">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="8b6cd-246">建议使用的替代方法是设置 :::no-loc(Cookie)::: 。SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="8b6cd-246">The recommended alternative is to set :::no-loc(Cookie):::.SecurePolicy.</span></span> |
| [<span data-ttu-id="8b6cd-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="8b6cd-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="8b6cd-248">指定是否取消生成 `X-Frame-Options` 标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="8b6cd-249">默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="8b6cd-250">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="8b6cd-251">有关详细信息，请参阅[ :::no-loc(Cookie)::: AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions)。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-251">For more information, see [:::no-loc(Cookie):::AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="8b6cd-252">通过 IAntiforgery 配置防伪功能</span><span class="sxs-lookup"><span data-stu-id="8b6cd-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="8b6cd-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 提供用于配置防伪功能的 API。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="8b6cd-254">`IAntiforgery` 可以在类的方法中请求 `Configure` `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="8b6cd-255">下面的示例使用应用的主页中的中间件来生成防伪令牌，并 :::no-loc(cookie)::: 使用本主题后面所述的默认角度命名约定将其作为 (发送) ：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a :::no-loc(cookie)::: (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="8b6cd-256">需要防伪验证</span><span class="sxs-lookup"><span data-stu-id="8b6cd-256">Require antiforgery validation</span></span>

<span data-ttu-id="8b6cd-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 是一个可应用于单个操作、控制器或全局的操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="8b6cd-258">将阻止对应用了此筛选器的操作发出的请求，除非该请求包含有效的防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="8b6cd-259">`ValidateAntiForgeryToken`特性要求对其标记的操作方法的请求进行标记，包括 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="8b6cd-260">如果该 `ValidateAntiForgeryToken` 特性应用于应用控制器，则可以用特性重写它 `IgnoreAntiforgeryToken` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="8b6cd-261">ASP.NET Core 不支持添加防伪令牌来自动获取请求。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="8b6cd-262">仅自动验证不安全 HTTP 方法的防伪令牌</span><span class="sxs-lookup"><span data-stu-id="8b6cd-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="8b6cd-263">ASP.NET Core 应用不会为安全 HTTP 方法 (GET、HEAD、OPTIONS 和 TRACE) 生成防伪标记。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="8b6cd-264">`ValidateAntiForgeryToken` `IgnoreAntiforgeryToken` 可以使用[AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性，而不是广泛应用该属性，然后使用特性重写它。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="8b6cd-265">此属性与属性的工作方式相同 `ValidateAntiForgeryToken` ，不同之处在于，它不需要使用以下 HTTP 方法发出的请求的令牌：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="8b6cd-266">GET</span><span class="sxs-lookup"><span data-stu-id="8b6cd-266">GET</span></span>
* <span data-ttu-id="8b6cd-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="8b6cd-267">HEAD</span></span>
* <span data-ttu-id="8b6cd-268">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="8b6cd-268">OPTIONS</span></span>
* <span data-ttu-id="8b6cd-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="8b6cd-269">TRACE</span></span>

<span data-ttu-id="8b6cd-270">我们建议将 `AutoValidateAntiforgeryToken` 广泛用于非 API 方案。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="8b6cd-271">这可确保默认情况下保护后操作。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="8b6cd-272">另一种方法是默认忽略防伪标记，除非 `ValidateAntiForgeryToken` 应用于各个操作方法。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="8b6cd-273">在这种情况下，更有可能在此方案中，不受错误阻止的 POST 操作方法，使应用容易受到 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="8b6cd-274">所有文章都应发送防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="8b6cd-275">Api 没有用于发送令牌的非部分的自动机制 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-275">APIs don't have an automatic mechanism for sending the non-:::no-loc(cookie)::: part of the token.</span></span> <span data-ttu-id="8b6cd-276">实现可能取决于客户端代码实现。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="8b6cd-277">下面显示了一些示例：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-277">Some examples are shown below:</span></span>

<span data-ttu-id="8b6cd-278">类级别的示例：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="8b6cd-279">全局示例：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b6cd-280">服务器.Addmvc 以 (options => 选项。筛选器。添加 (new AutoValidateAntiforgeryTokenAttribute ( # A3 # A4 # A5;</span><span class="sxs-lookup"><span data-stu-id="8b6cd-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="8b6cd-281">重写全局或控制器防伪属性</span><span class="sxs-lookup"><span data-stu-id="8b6cd-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="8b6cd-282">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)筛选器用于消除给定操作 (或控制器) 的防伪标记的需要。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="8b6cd-283">应用此筛选器时，此筛选器 `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` 将覆盖 (全局或控制器) 上的更高级别指定的筛选器和筛选器。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="8b6cd-284">身份验证后刷新令牌</span><span class="sxs-lookup"><span data-stu-id="8b6cd-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="8b6cd-285">用户通过将用户重定向到 "视图" 或 "页面" 页进行身份验证后，应刷新标记 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or :::no-loc(Razor)::: Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="8b6cd-286">JavaScript、AJAX 和 Spa</span><span class="sxs-lookup"><span data-stu-id="8b6cd-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="8b6cd-287">在传统的基于 HTML 的应用程序中，使用隐藏的窗体字段将防伪令牌传递给服务器。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="8b6cd-288">在基于 JavaScript 的新式应用和 Spa 中，许多请求是以编程方式进行的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="8b6cd-289">这些 AJAX 请求可能使用其他技术 (例如请求标头或 :::no-loc(cookie):::) 发送令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-289">These AJAX requests may use other techniques (such as request headers or :::no-loc(cookie):::s) to send the token.</span></span>

<span data-ttu-id="8b6cd-290">如果 :::no-loc(cookie)::: 使用来存储身份验证令牌，并在服务器上对 API 请求进行身份验证，则 CSRF 是一个潜在问题。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-290">If :::no-loc(cookie):::s are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="8b6cd-291">如果使用本地存储来存储令牌，可能会降低 CSRF 漏洞，因为本地存储中的值不会随每个请求自动发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="8b6cd-292">因此，使用本地存储在客户端上存储防伪令牌并将令牌作为请求标头进行发送是建议的方法。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="8b6cd-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8b6cd-293">JavaScript</span></span>

<span data-ttu-id="8b6cd-294">将 JavaScript 用于视图，可以使用视图中的服务创建令牌。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="8b6cd-295">将 [AspNetCore 防伪 IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 服务插入到视图中，并调用 [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="8b6cd-296">此方法无需直接处理服务器中的设置 :::no-loc(cookie)::: 或从客户端读取。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-296">This approach eliminates the need to deal directly with setting :::no-loc(cookie):::s from the server or reading them from the client.</span></span>

<span data-ttu-id="8b6cd-297">前面的示例使用 JavaScript 读取 AJAX POST 标头的隐藏字段值。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="8b6cd-298">JavaScript 还可以访问中的令牌 :::no-loc(cookie)::: ，并使用 :::no-loc(cookie)::: 的内容创建具有令牌值的标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-298">JavaScript can also access tokens in :::no-loc(cookie):::s and use the :::no-loc(cookie):::'s contents to create a header with the token's value.</span></span>

```csharp
context.Response.:::no-loc(Cookie):::s.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options { HttpOnly = false });
```

<span data-ttu-id="8b6cd-299">假设脚本请求将令牌发送到名为的标头 `X-CSRF-TOKEN` ，请将防伪服务配置为查找 `X-CSRF-TOKEN` 标头：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="8b6cd-300">下面的示例使用 JavaScript 通过适当的标头发出 AJAX 请求：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function get:::no-loc(Cookie):::(cname) {
    var name = cname + "=";
    var decoded:::no-loc(Cookie)::: = decodeURIComponent(document.:::no-loc(cookie):::);
    var ca = decoded:::no-loc(Cookie):::.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = get:::no-loc(Cookie):::("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="8b6cd-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="8b6cd-301">AngularJS</span></span>

<span data-ttu-id="8b6cd-302">AngularJS 使用约定来寻址 CSRF。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="8b6cd-303">如果服务器发送 :::no-loc(cookie)::: 具有名称的，则 `XSRF-TOKEN` AngularJS `$http` 服务会在向 :::no-loc(cookie)::: 服务器发送请求时将该值添加到标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-303">If the server sends a :::no-loc(cookie)::: with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the :::no-loc(cookie)::: value to a header when it sends a request to the server.</span></span> <span data-ttu-id="8b6cd-304">此过程是自动进行的。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-304">This process is automatic.</span></span> <span data-ttu-id="8b6cd-305">不需要在客户端中显式设置标头。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="8b6cd-306">标头的名称为 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="8b6cd-307">服务器应检测此标头并验证其内容。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="8b6cd-308">对于在应用程序启动时使用此约定的 ASP.NET Core API：</span><span class="sxs-lookup"><span data-stu-id="8b6cd-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="8b6cd-309">将你的应用程序配置为在调用中提供标记 :::no-loc(cookie)::: `XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-309">Configure your app to provide a token in a :::no-loc(cookie)::: called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="8b6cd-310">配置防伪服务以查找名为的标头 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="8b6cd-311">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b6cd-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="8b6cd-312">扩展防伪</span><span class="sxs-lookup"><span data-stu-id="8b6cd-312">Extend antiforgery</span></span>

<span data-ttu-id="8b6cd-313">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)类型允许开发人员通过往返每个标记中的其他数据来扩展反 CSRF 系统的行为。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="8b6cd-314">每次生成字段标记时都会调用 [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 方法，并且返回值嵌入到生成的标记中。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="8b6cd-315">在验证令牌时，实施者可以返回时间戳、nonce 或任何其他值，然后调用 [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 来验证此数据。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="8b6cd-316">客户端的用户名已嵌入到生成的令牌中，因此不需要包含此信息。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="8b6cd-317">如果令牌包含补充数据但未 `IAntiForgeryAdditionalDataProvider` 配置，则不会验证补充数据。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b6cd-318">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b6cd-318">Additional resources</span></span>

* <span data-ttu-id="8b6cd-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) [打开 Web 应用程序安全项目](https://www.owasp.org/index.php/Main_Page) (OWASP) 。</span><span class="sxs-lookup"><span data-stu-id="8b6cd-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
