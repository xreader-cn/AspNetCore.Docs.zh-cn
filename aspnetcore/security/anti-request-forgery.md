---
title: 在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击
author: steve-smith
description: 了解如何防止攻击，恶意网站可以影响客户端浏览器和应用之间的交互的 web 应用。
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 88a2d127407378b9e83df7f48b1938ed081f9bb2
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208523"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="7506e-103">在 ASP.NET Core 防止跨站点请求伪造 (XSRF/CSRF) 攻击</span><span class="sxs-lookup"><span data-stu-id="7506e-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="7506e-104">通过[Steve Smith](https://ardalis.com/)， [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)，和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7506e-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7506e-105">跨站点请求伪造 (也称为 XSRF 或 CSRF，读作 *，请参阅冲浪*) 是针对恶意网站凭此可以影响客户端浏览器和信任的 web 应用之间的交互的 web 托管的应用程序的攻击浏览器。</span><span class="sxs-lookup"><span data-stu-id="7506e-105">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="7506e-106">这些攻击是可能的因为 web 浏览器将自动随每个请求某些类型的身份验证令牌发送到网站。</span><span class="sxs-lookup"><span data-stu-id="7506e-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="7506e-107">这种形式的攻击是也称为*一键式攻击*或*会话行进*因为此攻击利用用户的身份验证会话。</span><span class="sxs-lookup"><span data-stu-id="7506e-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="7506e-108">CSRF 攻击的示例：</span><span class="sxs-lookup"><span data-stu-id="7506e-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="7506e-109">用户登录到`www.good-banking-site.com`使用窗体身份验证。</span><span class="sxs-lookup"><span data-stu-id="7506e-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="7506e-110">服务器对用户进行身份验证，并会发出包含身份验证 cookie 的响应。</span><span class="sxs-lookup"><span data-stu-id="7506e-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="7506e-111">站点是易受到攻击，因为它信任的任何请求都收到与有效的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="7506e-112">在用户访问恶意站点， `www.bad-crook-site.com`。</span><span class="sxs-lookup"><span data-stu-id="7506e-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="7506e-113">恶意网站`www.bad-crook-site.com`，包含 HTML 窗体如下所示：</span><span class="sxs-lookup"><span data-stu-id="7506e-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="7506e-114">请注意，窗体的`action`帖子到易受攻击的站点上，而不是恶意站点。</span><span class="sxs-lookup"><span data-stu-id="7506e-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="7506e-115">这是 CSRF 的"跨站点"部分。</span><span class="sxs-lookup"><span data-stu-id="7506e-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="7506e-116">用户选择提交按钮。</span><span class="sxs-lookup"><span data-stu-id="7506e-116">The user selects the submit button.</span></span> <span data-ttu-id="7506e-117">浏览器发出请求，并会自动包括请求的域的身份验证 cookie `www.good-banking-site.com`。</span><span class="sxs-lookup"><span data-stu-id="7506e-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="7506e-118">运行该请求`www.good-banking-site.com`与用户的身份验证上下文的服务器，并且可以执行身份验证的用户可以执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="7506e-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="7506e-119">除了其中用户选择按钮以提交窗体方案中，恶意网站可以：</span><span class="sxs-lookup"><span data-stu-id="7506e-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="7506e-120">运行自动提交窗体的脚本。</span><span class="sxs-lookup"><span data-stu-id="7506e-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="7506e-121">AJAX 请求用以发送窗体提交。</span><span class="sxs-lookup"><span data-stu-id="7506e-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="7506e-122">隐藏窗体使用 CSS。</span><span class="sxs-lookup"><span data-stu-id="7506e-122">Hide the form using CSS.</span></span>

<span data-ttu-id="7506e-123">这些替代方案不需要任何操作或从用户而不是最初访问恶意网站的输入。</span><span class="sxs-lookup"><span data-stu-id="7506e-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="7506e-124">使用 HTTPS，则不会阻止 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="7506e-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="7506e-125">可以发送恶意站点 `https://www.good-banking-site.com/` 请求一样方便它可以发送不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="7506e-126">一些攻击目标终结点的响应 GET 请求，在这种情况下的图像标记可用于执行的操作。</span><span class="sxs-lookup"><span data-stu-id="7506e-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="7506e-127">这种形式的攻击的允许的映像，但阻止 JavaScript 论坛站点上很常见。</span><span class="sxs-lookup"><span data-stu-id="7506e-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="7506e-128">应用程序来更改的状态的 GET 请求，在其中更改变量或资源，是很容易受到恶意攻击。</span><span class="sxs-lookup"><span data-stu-id="7506e-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="7506e-129">**更改状态的 GET 请求将不安全。最佳做法是永远不会更改上的 GET 请求的状态。**</span><span class="sxs-lookup"><span data-stu-id="7506e-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="7506e-130">CSRF 攻击是可能使用 cookie 进行身份验证，因为 web 应用：</span><span class="sxs-lookup"><span data-stu-id="7506e-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="7506e-131">浏览器存储 cookie 颁发的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="7506e-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="7506e-132">存储的 cookie 包括用于身份验证的用户的会话 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="7506e-133">浏览器发送的所有 cookie 与域关联到 web 应用而不考虑对应用程序的请求如何生成在浏览器中的每个请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="7506e-134">但是，到限于 CSRF 攻击利用 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="7506e-135">例如，基本和摘要式身份验证也是易受攻击的。</span><span class="sxs-lookup"><span data-stu-id="7506e-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="7506e-136">浏览器使用基本或摘要式身份验证的用户登录后，直到在会话自动发送凭据&dagger;结束。</span><span class="sxs-lookup"><span data-stu-id="7506e-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="7506e-137">&dagger;在此上下文中，*会话*指的是在此期间用户进行身份验证的客户端会话。</span><span class="sxs-lookup"><span data-stu-id="7506e-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="7506e-138">它是与服务器端会话无关或[ASP.NET Core 会话中间件](xref:fundamentals/app-state)。</span><span class="sxs-lookup"><span data-stu-id="7506e-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="7506e-139">用户可以通过采取预防措施来防止 CSRF 漏洞：</span><span class="sxs-lookup"><span data-stu-id="7506e-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="7506e-140">从 web 应用时将它们完成相应操作进行签名。</span><span class="sxs-lookup"><span data-stu-id="7506e-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="7506e-141">定期清除浏览器 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="7506e-142">但是，CSRF 漏洞从根本上说是 web 应用，而不是最终用户的问题。</span><span class="sxs-lookup"><span data-stu-id="7506e-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="7506e-143">身份验证基础知识</span><span class="sxs-lookup"><span data-stu-id="7506e-143">Authentication fundamentals</span></span>

<span data-ttu-id="7506e-144">基于 cookie 的身份验证是常用形式的身份验证。</span><span class="sxs-lookup"><span data-stu-id="7506e-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="7506e-145">基于令牌的身份验证系统越来越流行，尤其是对于单页面应用程序 (Spa)。</span><span class="sxs-lookup"><span data-stu-id="7506e-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="7506e-146">基于 cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="7506e-146">Cookie-based authentication</span></span>

<span data-ttu-id="7506e-147">当用户通过使用其用户名和密码时，它们被颁发一个令牌，其中包含可用于身份验证和授权的身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="7506e-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="7506e-148">作为一个附带的每个请求客户端的 cookie 会使存储令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="7506e-149">由 Cookie 身份验证中间件执行生成和验证此 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="7506e-150">[中间件](xref:fundamentals/middleware/index)的加密 cookie 序列化为一个用户主体。</span><span class="sxs-lookup"><span data-stu-id="7506e-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="7506e-151">在后续请求中，中间件将验证 cookie、 重新创建主体，并将分配到主体[用户](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)的属性[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)。</span><span class="sxs-lookup"><span data-stu-id="7506e-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="7506e-152">基于令牌的身份验证</span><span class="sxs-lookup"><span data-stu-id="7506e-152">Token-based authentication</span></span>

<span data-ttu-id="7506e-153">当用户进行身份验证时，它们被颁发的令牌 （不防伪令牌）。</span><span class="sxs-lookup"><span data-stu-id="7506e-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="7506e-154">该令牌包含的窗体中的用户信息[声明](/dotnet/framework/security/claims-based-identity-model)或指向用户状态保留在应用程序的应用程序的引用标记。</span><span class="sxs-lookup"><span data-stu-id="7506e-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="7506e-155">当用户尝试访问资源需要身份验证时，则会将令牌发送到其他 authorization 标头中的持有者令牌的窗体应用。</span><span class="sxs-lookup"><span data-stu-id="7506e-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="7506e-156">这使得应用程序无状态。</span><span class="sxs-lookup"><span data-stu-id="7506e-156">This makes the app stateless.</span></span> <span data-ttu-id="7506e-157">在每个后续请求的令牌请求中传递的服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="7506e-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="7506e-158">此令牌不是*加密*; 它具有*编码*。</span><span class="sxs-lookup"><span data-stu-id="7506e-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="7506e-159">在服务器上，将解码令牌来访问其信息。</span><span class="sxs-lookup"><span data-stu-id="7506e-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="7506e-160">若要在后续请求中发送令牌，请在浏览器的本地存储中存储令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="7506e-161">如果该令牌存储在浏览器的本地存储，则不会担心 CSRF 的漏洞。</span><span class="sxs-lookup"><span data-stu-id="7506e-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="7506e-162">CSRF 令牌存储在 cookie 中时是一个问题。</span><span class="sxs-lookup"><span data-stu-id="7506e-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="7506e-163">在一个域上托管的多个应用</span><span class="sxs-lookup"><span data-stu-id="7506e-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="7506e-164">共享宿主环境为易受到会话劫持、 登录名 CSRF 和其他攻击。</span><span class="sxs-lookup"><span data-stu-id="7506e-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="7506e-165">尽管`example1.contoso.net`并`example2.contoso.net`是不同的主机，在主机之间没有隐式信任关系`*.contoso.net`域。</span><span class="sxs-lookup"><span data-stu-id="7506e-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="7506e-166">此隐式信任关系允许可能不受信任的主机会影响彼此的 cookie （控制 AJAX 请求的同域策略不一定适用于 HTTP cookie）。</span><span class="sxs-lookup"><span data-stu-id="7506e-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="7506e-167">通过不共享域，可以防止攻击，可利用同一个域上托管的应用程序之间的受信任的 cookie。</span><span class="sxs-lookup"><span data-stu-id="7506e-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="7506e-168">在其自己的域中托管每个应用，则时利用任何隐式 cookie 信任关系。</span><span class="sxs-lookup"><span data-stu-id="7506e-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="7506e-169">ASP.NET Core antiforgery 配置</span><span class="sxs-lookup"><span data-stu-id="7506e-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="7506e-170">ASP.NET Core 实现 antiforgery 使用[ASP.NET Core 数据保护](xref:security/data-protection/introduction)。</span><span class="sxs-lookup"><span data-stu-id="7506e-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="7506e-171">数据保护堆栈必须配置为在服务器场中工作。</span><span class="sxs-lookup"><span data-stu-id="7506e-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="7506e-172">请参阅[配置数据保护](xref:security/data-protection/configuration/overview)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="7506e-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="7506e-173">在 ASP.NET Core 2.0 或更高版本， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antiforgery 令牌注入 HTML 窗体元素。</span><span class="sxs-lookup"><span data-stu-id="7506e-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="7506e-174">Razor 文件中的以下标记自动生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="7506e-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="7506e-175">同样， [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)默认情况下生成防伪令牌，如果窗体的方法不是 GET。</span><span class="sxs-lookup"><span data-stu-id="7506e-175">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="7506e-176">HTML 窗体元素自动生成的防伪令牌发生时`<form>`标记包含`method="post"`属性和以下任一条件成立：</span><span class="sxs-lookup"><span data-stu-id="7506e-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="7506e-177">操作属性为空 (`action=""`)。</span><span class="sxs-lookup"><span data-stu-id="7506e-177">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="7506e-178">操作属性不提供 (`<form method="post">`)。</span><span class="sxs-lookup"><span data-stu-id="7506e-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="7506e-179">可以禁用自动生成的防伪标记 HTML 窗体元素：</span><span class="sxs-lookup"><span data-stu-id="7506e-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="7506e-180">显式禁用防伪令牌，且`asp-antiforgery`属性：</span><span class="sxs-lookup"><span data-stu-id="7506e-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="7506e-181">窗体元素是选择向外的标记帮助程序使用标记帮助程序[！ 选择退出符号](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="7506e-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="7506e-182">删除`FormTagHelper`从视图。</span><span class="sxs-lookup"><span data-stu-id="7506e-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="7506e-183">`FormTagHelper`可以通过将以下指令添加到 Razor 视图从视图中删除：</span><span class="sxs-lookup"><span data-stu-id="7506e-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="7506e-184">[Razor 页面](xref:razor-pages/index)会自动防范 XSRF/CSRF。</span><span class="sxs-lookup"><span data-stu-id="7506e-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="7506e-185">有关详细信息，请参阅[XSRF/CSRF 和 Razor 页面](xref:razor-pages/index#xsrf)。</span><span class="sxs-lookup"><span data-stu-id="7506e-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="7506e-186">为抵御 CSRF 攻击最常用的方法是使用*同步器标记模式*(STP)。</span><span class="sxs-lookup"><span data-stu-id="7506e-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="7506e-187">当用户请求的页面包含窗体数据使用 STP:</span><span class="sxs-lookup"><span data-stu-id="7506e-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="7506e-188">服务器发送到客户端的当前用户的标识相关联的令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="7506e-189">客户端返回将令牌发送到服务器进行验证。</span><span class="sxs-lookup"><span data-stu-id="7506e-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="7506e-190">如果服务器收到与经过身份验证的用户的标识不匹配的令牌，将拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="7506e-191">该令牌唯一且不可预测。</span><span class="sxs-lookup"><span data-stu-id="7506e-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="7506e-192">该令牌还可用于确保正确序列化的一系列的请求 (例如，确保请求序列的： 第 1 页&ndash;第 2 页&ndash;第 3 页)。</span><span class="sxs-lookup"><span data-stu-id="7506e-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="7506e-193">ASP.NET Core MVC 和 Razor 页模板中的窗体的所有生成 antiforgery 令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="7506e-194">以下两个视图的示例生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="7506e-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="7506e-195">显式添加到防伪令牌`<form>`而无需使用标记帮助程序与 HTML 帮助程序元素[ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="7506e-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="7506e-196">在每个前面的情况下，ASP.NET Core 添加类似于以下一个隐藏的表单字段：</span><span class="sxs-lookup"><span data-stu-id="7506e-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="7506e-197">ASP.NET Core 包括三个[筛选器](xref:mvc/controllers/filters)来处理 antiforgery 令牌：</span><span class="sxs-lookup"><span data-stu-id="7506e-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="7506e-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="7506e-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="7506e-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="7506e-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="7506e-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="7506e-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="7506e-201">防伪选项</span><span class="sxs-lookup"><span data-stu-id="7506e-201">Antiforgery options</span></span>

<span data-ttu-id="7506e-202">自定义[防伪选项](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7506e-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="7506e-203">&dagger;设置防伪`Cookie`属性使用的属性[CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)类。</span><span class="sxs-lookup"><span data-stu-id="7506e-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="7506e-204">选项</span><span class="sxs-lookup"><span data-stu-id="7506e-204">Option</span></span> | <span data-ttu-id="7506e-205">描述</span><span class="sxs-lookup"><span data-stu-id="7506e-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7506e-206">Cookie</span><span class="sxs-lookup"><span data-stu-id="7506e-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="7506e-207">确定用于创建防伪 cookie 的设置。</span><span class="sxs-lookup"><span data-stu-id="7506e-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="7506e-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="7506e-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="7506e-209">防伪系统用于呈现防伪令牌在视图中的隐藏的窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="7506e-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="7506e-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="7506e-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="7506e-211">防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="7506e-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="7506e-212">如果`null`，系统会认为只有窗体数据。</span><span class="sxs-lookup"><span data-stu-id="7506e-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="7506e-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="7506e-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="7506e-214">指定是否禁止显示生成`X-Frame-Options`标头。</span><span class="sxs-lookup"><span data-stu-id="7506e-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="7506e-215">默认情况下，值为"SAMEORIGIN"生成标头。</span><span class="sxs-lookup"><span data-stu-id="7506e-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="7506e-216">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="7506e-216">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="7506e-217">选项</span><span class="sxs-lookup"><span data-stu-id="7506e-217">Option</span></span> | <span data-ttu-id="7506e-218">描述</span><span class="sxs-lookup"><span data-stu-id="7506e-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7506e-219">Cookie</span><span class="sxs-lookup"><span data-stu-id="7506e-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="7506e-220">确定用于创建防伪 cookie 的设置。</span><span class="sxs-lookup"><span data-stu-id="7506e-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="7506e-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="7506e-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="7506e-222">Cookie 的域。</span><span class="sxs-lookup"><span data-stu-id="7506e-222">The domain of the cookie.</span></span> <span data-ttu-id="7506e-223">默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="7506e-223">Defaults to `null`.</span></span> <span data-ttu-id="7506e-224">此属性已过时，将在未来版本中删除。</span><span class="sxs-lookup"><span data-stu-id="7506e-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7506e-225">建议的替代项是 Cookie.Domain。</span><span class="sxs-lookup"><span data-stu-id="7506e-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="7506e-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="7506e-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="7506e-227">Cookie 的名称。</span><span class="sxs-lookup"><span data-stu-id="7506e-227">The name of the cookie.</span></span> <span data-ttu-id="7506e-228">如果未设置，则系统生成唯一的名称开头[DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("。AspNetCore.Antiforgery。") 下。</span><span class="sxs-lookup"><span data-stu-id="7506e-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="7506e-229">此属性已过时，将在未来版本中删除。</span><span class="sxs-lookup"><span data-stu-id="7506e-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7506e-230">建议的替代项是 Cookie.Name。</span><span class="sxs-lookup"><span data-stu-id="7506e-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="7506e-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="7506e-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="7506e-232">设置 cookie 的路径。</span><span class="sxs-lookup"><span data-stu-id="7506e-232">The path set on the cookie.</span></span> <span data-ttu-id="7506e-233">此属性已过时，将在未来版本中删除。</span><span class="sxs-lookup"><span data-stu-id="7506e-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7506e-234">建议的替代项是 Cookie.Path。</span><span class="sxs-lookup"><span data-stu-id="7506e-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="7506e-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="7506e-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="7506e-236">防伪系统用于呈现防伪令牌在视图中的隐藏的窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="7506e-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="7506e-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="7506e-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="7506e-238">防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="7506e-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="7506e-239">如果`null`，系统会认为只有窗体数据。</span><span class="sxs-lookup"><span data-stu-id="7506e-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="7506e-240">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="7506e-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="7506e-241">指定是否由防伪系统需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7506e-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="7506e-242">如果`true`，非 HTTPS 发出的请求失败。</span><span class="sxs-lookup"><span data-stu-id="7506e-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="7506e-243">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="7506e-243">Defaults to `false`.</span></span> <span data-ttu-id="7506e-244">此属性已过时，将在未来版本中删除。</span><span class="sxs-lookup"><span data-stu-id="7506e-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7506e-245">建议的替代项是设置 Cookie.SecurePolicy。</span><span class="sxs-lookup"><span data-stu-id="7506e-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="7506e-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="7506e-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="7506e-247">指定是否禁止显示生成`X-Frame-Options`标头。</span><span class="sxs-lookup"><span data-stu-id="7506e-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="7506e-248">默认情况下，值为"SAMEORIGIN"生成标头。</span><span class="sxs-lookup"><span data-stu-id="7506e-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="7506e-249">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="7506e-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="7506e-250">有关详细信息，请参阅[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。</span><span class="sxs-lookup"><span data-stu-id="7506e-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="7506e-251">配置与 IAntiforgery 防伪功能</span><span class="sxs-lookup"><span data-stu-id="7506e-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="7506e-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)提供 API 来配置防伪功能。</span><span class="sxs-lookup"><span data-stu-id="7506e-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="7506e-253">`IAntiforgery` 可以在请求`Configure`方法的`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="7506e-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="7506e-254">以下示例使用中间件应用程序的主页上生成防伪令牌并将其在响应中发送作为 cookie （使用本主题后面所述的默认 Angular 命名约定）：</span><span class="sxs-lookup"><span data-stu-id="7506e-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="7506e-255">需要防伪验证</span><span class="sxs-lookup"><span data-stu-id="7506e-255">Require antiforgery validation</span></span>

<span data-ttu-id="7506e-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)是操作筛选器可应用到单个操作，在控制器或全局范围内。</span><span class="sxs-lookup"><span data-stu-id="7506e-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="7506e-257">除非请求包含有效的防伪令牌，将阻止对已应用此筛选器的操作发出的请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="7506e-258">`ValidateAntiForgeryToken`属性需要对操作方法请求修饰，包括 HTTP GET 请求令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="7506e-259">如果`ValidateAntiForgeryToken`特性应用于应用程序的控制器，它可以替换`IgnoreAntiforgeryToken`属性。</span><span class="sxs-lookup"><span data-stu-id="7506e-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="7506e-260">ASP.NET Core 不支持自动将 antiforgery 令牌添加到 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="7506e-261">自动验证防伪令牌仅不安全的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="7506e-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="7506e-262">ASP.NET Core 应用不生成 antiforgery 令牌进行安全的 HTTP 方法 （GET、 HEAD、 选项和跟踪）。</span><span class="sxs-lookup"><span data-stu-id="7506e-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="7506e-263">而不是广泛应用`ValidateAntiForgeryToken`属性，然后将其与重写`IgnoreAntiforgeryToken`属性， [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性可用。</span><span class="sxs-lookup"><span data-stu-id="7506e-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="7506e-264">此属性工作原理相同于`ValidateAntiForgeryToken`属性，只不过它不需要使用以下 HTTP 方法发出的请求令牌：</span><span class="sxs-lookup"><span data-stu-id="7506e-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="7506e-265">GET</span><span class="sxs-lookup"><span data-stu-id="7506e-265">GET</span></span>
* <span data-ttu-id="7506e-266">HEAD</span><span class="sxs-lookup"><span data-stu-id="7506e-266">HEAD</span></span>
* <span data-ttu-id="7506e-267">选项</span><span class="sxs-lookup"><span data-stu-id="7506e-267">OPTIONS</span></span>
* <span data-ttu-id="7506e-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="7506e-268">TRACE</span></span>

<span data-ttu-id="7506e-269">我们建议使用`AutoValidateAntiforgeryToken`广泛用于非 API 方案。</span><span class="sxs-lookup"><span data-stu-id="7506e-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="7506e-270">这可确保默认被保护 POST 操作。</span><span class="sxs-lookup"><span data-stu-id="7506e-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="7506e-271">替代方法是忽略防伪令牌，默认情况下，除非`ValidateAntiForgeryToken`应用于各个操作方法。</span><span class="sxs-lookup"><span data-stu-id="7506e-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="7506e-272">它更有可能在此方案中保留的 POST 操作方法不受保护错误地使应用程序容易受到 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="7506e-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="7506e-273">所有Post请求都应发送防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="7506e-274">Api 没有发送令牌的非 cookie 一部分的自动机制。</span><span class="sxs-lookup"><span data-stu-id="7506e-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="7506e-275">实现可能取决于客户端代码实现。</span><span class="sxs-lookup"><span data-stu-id="7506e-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="7506e-276">一些示例如下所示：</span><span class="sxs-lookup"><span data-stu-id="7506e-276">Some examples are shown below:</span></span>

<span data-ttu-id="7506e-277">类级别的示例：</span><span class="sxs-lookup"><span data-stu-id="7506e-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="7506e-278">全局示例：</span><span class="sxs-lookup"><span data-stu-id="7506e-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="7506e-279">重写全局或控制器防伪特性</span><span class="sxs-lookup"><span data-stu-id="7506e-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="7506e-280">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)使用筛选器不再需要给定操作 （或控制器） 的防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="7506e-281">当应用时，此筛选器将重写`ValidateAntiForgeryToken`和`AutoValidateAntiforgeryToken`（全局或控制器上） 在较高级别指定的筛选器。</span><span class="sxs-lookup"><span data-stu-id="7506e-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="7506e-282">刷新令牌的身份验证后</span><span class="sxs-lookup"><span data-stu-id="7506e-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="7506e-283">用户进行身份验证通过将用户重定向到一个视图或 Razor 页面页后，应刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="7506e-284">JavaScript、 AJAX 和 Spa</span><span class="sxs-lookup"><span data-stu-id="7506e-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="7506e-285">在传统的基于 HTML 的应用中，防伪令牌将传递给使用隐藏的表单域的服务器。</span><span class="sxs-lookup"><span data-stu-id="7506e-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="7506e-286">在基于 JavaScript 的新型应用程序和 Spa 中，以编程方式进行很多请求。</span><span class="sxs-lookup"><span data-stu-id="7506e-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="7506e-287">这些 AJAX 请求可以使用其他技术 （如请求标头或 cookie） 来发送令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="7506e-288">如果使用 cookie 来存储身份验证令牌，并在服务器上的 API 请求进行身份验证，CSRF 是一个潜在的问题。</span><span class="sxs-lookup"><span data-stu-id="7506e-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="7506e-289">如果本地存储用于存储令牌，因为从本地存储的值不会自动发送到每个请求的服务器可能会缓解 CSRF 的漏洞。</span><span class="sxs-lookup"><span data-stu-id="7506e-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="7506e-290">因此，使用本地存储来存储客户端和发送令牌作为请求标头是建议的方法上的防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="7506e-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="7506e-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7506e-291">JavaScript</span></span>

<span data-ttu-id="7506e-292">使用 JavaScript 与视图，该令牌可以创建使用从视图中的服务。</span><span class="sxs-lookup"><span data-stu-id="7506e-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="7506e-293">注入[Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)到视图并调用服务[GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="7506e-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="7506e-294">此方法无需直接处理从服务器设置 cookie 或从客户端读取它们。</span><span class="sxs-lookup"><span data-stu-id="7506e-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="7506e-295">前面的示例中使用 JavaScript 为 POST 的 AJAX 标头中读取的隐藏的字段值。</span><span class="sxs-lookup"><span data-stu-id="7506e-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="7506e-296">JavaScript 还可以访问 cookie 中的令牌和 cookie 的内容使用令牌的值创建的标头。</span><span class="sxs-lookup"><span data-stu-id="7506e-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="7506e-297">假设脚本请求调用的标头中发送令牌`X-CSRF-TOKEN`，配置要寻找的防伪服务`X-CSRF-TOKEN`标头：</span><span class="sxs-lookup"><span data-stu-id="7506e-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="7506e-298">以下示例使用 JavaScript 来发出 AJAX 请求与相应的标头：</span><span class="sxs-lookup"><span data-stu-id="7506e-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
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

var csrfToken = getCookie("CSRF-TOKEN");

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

### <a name="angularjs"></a><span data-ttu-id="7506e-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="7506e-299">AngularJS</span></span>

<span data-ttu-id="7506e-300">AngularJS 使用到地址 CSRF 的约定。</span><span class="sxs-lookup"><span data-stu-id="7506e-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="7506e-301">如果服务器发送包含名称的 cookie `XSRF-TOKEN`，AngularJS`$http`服务将 cookie 值添加到标头时它将请求发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="7506e-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="7506e-302">此过程是自动的。</span><span class="sxs-lookup"><span data-stu-id="7506e-302">This process is automatic.</span></span> <span data-ttu-id="7506e-303">标头不需要显式设置在客户端。</span><span class="sxs-lookup"><span data-stu-id="7506e-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="7506e-304">标头名称是`X-XSRF-TOKEN`。</span><span class="sxs-lookup"><span data-stu-id="7506e-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="7506e-305">服务器应检测此标头，并验证其内容。</span><span class="sxs-lookup"><span data-stu-id="7506e-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="7506e-306">ASP.NET Core api 以使用这种规定应用程序启动：</span><span class="sxs-lookup"><span data-stu-id="7506e-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="7506e-307">将应用配置为提供的令牌在 cookie 中名为`XSRF-TOKEN`。</span><span class="sxs-lookup"><span data-stu-id="7506e-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="7506e-308">查找名为标头将防伪服务配置`X-XSRF-TOKEN`。</span><span class="sxs-lookup"><span data-stu-id="7506e-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
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

<span data-ttu-id="7506e-309">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7506e-309">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="7506e-310">扩展防伪</span><span class="sxs-lookup"><span data-stu-id="7506e-310">Extend antiforgery</span></span>

<span data-ttu-id="7506e-311">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)类型允许开发人员能够通过往返过程中的每个标记的其他数据来扩展反 CSRF 系统的行为。</span><span class="sxs-lookup"><span data-stu-id="7506e-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="7506e-312">[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)每次调用方法生成的字段标记，并返回值嵌入生成的令牌中。</span><span class="sxs-lookup"><span data-stu-id="7506e-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="7506e-313">实现器可以返回一个时间戳、 nonce 或任何其他值，然后调用[ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)验证令牌时验证此数据。</span><span class="sxs-lookup"><span data-stu-id="7506e-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="7506e-314">客户端的用户名已嵌入在生成的令牌中，因此无需包含此信息。</span><span class="sxs-lookup"><span data-stu-id="7506e-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="7506e-315">如果令牌包含补充数据但不是`IAntiForgeryAdditionalDataProvider`是配置，并不验证补充数据。</span><span class="sxs-lookup"><span data-stu-id="7506e-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7506e-316">其他资源</span><span class="sxs-lookup"><span data-stu-id="7506e-316">Additional resources</span></span>

* <span data-ttu-id="7506e-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))上[打开 Web 应用程序安全项目](https://www.owasp.org/index.php/Main_Page)(OWASP)。</span><span class="sxs-lookup"><span data-stu-id="7506e-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
