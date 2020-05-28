---
<span data-ttu-id="fb806-101">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-102">'Blazor'</span></span>
- <span data-ttu-id="fb806-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-103">'Identity'</span></span>
- <span data-ttu-id="fb806-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-105">'Razor'</span></span>
- <span data-ttu-id="fb806-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="fb806-107">在 ASP.NET Core 中阻止跨站点请求伪造（XSRF/CSRF）攻击</span><span class="sxs-lookup"><span data-stu-id="fb806-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="fb806-108">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fb806-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fb806-109">跨站点请求伪造（也称为 XSRF 或 CSRF）是一种针对 web 托管应用的攻击，恶意 web 应用可能会影响客户端浏览器与信任该浏览器的 web 应用之间的交互。</span><span class="sxs-lookup"><span data-stu-id="fb806-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="fb806-110">这些攻击是可能的，因为 web 浏览器会自动向网站发送每个请求的身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="fb806-111">这种形式的攻击也称为*一键式攻击*或*会话 riding* ，因为攻击利用用户以前的经过身份验证的会话。</span><span class="sxs-lookup"><span data-stu-id="fb806-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="fb806-112">CSRF 攻击的示例：</span><span class="sxs-lookup"><span data-stu-id="fb806-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="fb806-113">用户 `www.good-banking-site.com` 使用窗体身份验证登录。</span><span class="sxs-lookup"><span data-stu-id="fb806-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="fb806-114">服务器对用户进行身份验证，并发出包含身份验证 cookie 的响应。</span><span class="sxs-lookup"><span data-stu-id="fb806-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="fb806-115">此站点容易受到攻击，因为它信任使用有效的身份验证 cookie 接收的任何请求。</span><span class="sxs-lookup"><span data-stu-id="fb806-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="fb806-116">用户访问恶意网站 `www.bad-crook-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="fb806-117">恶意站点包含如下 `www.bad-crook-site.com` 所示的 HTML 格式：</span><span class="sxs-lookup"><span data-stu-id="fb806-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="fb806-118">请注意，表单 `action` 会发布到易受攻击的站点，而不是恶意站点。</span><span class="sxs-lookup"><span data-stu-id="fb806-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="fb806-119">这是 CSRF 的 "跨站点" 部分。</span><span class="sxs-lookup"><span data-stu-id="fb806-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="fb806-120">用户选择 "提交" 按钮。</span><span class="sxs-lookup"><span data-stu-id="fb806-120">The user selects the submit button.</span></span> <span data-ttu-id="fb806-121">浏览器发出请求并自动包含请求域的身份验证 cookie `www.good-banking-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="fb806-122">请求在 `www.good-banking-site.com` 服务器上使用用户的身份验证上下文运行，并可执行允许用户执行的任何操作。</span><span class="sxs-lookup"><span data-stu-id="fb806-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="fb806-123">除了用户选择按钮以提交窗体的情况外，恶意网站还可以：</span><span class="sxs-lookup"><span data-stu-id="fb806-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="fb806-124">运行自动提交窗体的脚本。</span><span class="sxs-lookup"><span data-stu-id="fb806-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="fb806-125">以 AJAX 请求形式发送窗体提交。</span><span class="sxs-lookup"><span data-stu-id="fb806-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="fb806-126">使用 CSS 隐藏窗体。</span><span class="sxs-lookup"><span data-stu-id="fb806-126">Hide the form using CSS.</span></span>

<span data-ttu-id="fb806-127">除最初访问恶意网站外，这些备选方案不需要用户执行任何操作或输入。</span><span class="sxs-lookup"><span data-stu-id="fb806-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="fb806-128">使用 HTTPS 不会阻止 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="fb806-129">恶意站点可以发送请求，就像发送不 `https://www.good-banking-site.com/` 安全请求一样容易。</span><span class="sxs-lookup"><span data-stu-id="fb806-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="fb806-130">某些攻击目标是响应 GET 请求的终结点，在这种情况下，可以使用图像标记执行操作。</span><span class="sxs-lookup"><span data-stu-id="fb806-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="fb806-131">这种形式的攻击在允许图像但阻止 JavaScript 的论坛站点上很常见。</span><span class="sxs-lookup"><span data-stu-id="fb806-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="fb806-132">在 GET 请求上更改状态的应用（其中变量或资源被更改）很容易受到恶意攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="fb806-133">**更改状态的 GET 请求是不安全的。最佳做法是从不更改 GET 请求的状态。**</span><span class="sxs-lookup"><span data-stu-id="fb806-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="fb806-134">对于使用 cookie 进行身份验证的 web 应用，可能会受到 CSRF 攻击，因为：</span><span class="sxs-lookup"><span data-stu-id="fb806-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="fb806-135">浏览器存储 web 应用颁发的 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="fb806-136">存储的 cookie 包括经过身份验证的用户的会话 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="fb806-137">无论在浏览器中生成应用程序请求的方式如何，浏览器都会将与域关联的所有 cookie 发送到 web 应用。</span><span class="sxs-lookup"><span data-stu-id="fb806-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="fb806-138">但是，CSRF 攻击并不局限于利用 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="fb806-139">例如，基本身份验证和摘要式身份验证也容易受到攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="fb806-140">用户使用基本或摘要式身份验证登录后，浏览器会自动发送凭据，直到会话 &dagger; 结束。</span><span class="sxs-lookup"><span data-stu-id="fb806-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="fb806-141">&dagger;在这种情况下，*会话*是指在其中对用户进行身份验证的客户端会话。</span><span class="sxs-lookup"><span data-stu-id="fb806-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="fb806-142">它与服务器端会话或[ASP.NET Core 会话中间件](xref:fundamentals/app-state)无关。</span><span class="sxs-lookup"><span data-stu-id="fb806-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="fb806-143">用户可以采取预防措施来防止 CSRF 漏洞：</span><span class="sxs-lookup"><span data-stu-id="fb806-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="fb806-144">使用完 web 应用后，将其注销。</span><span class="sxs-lookup"><span data-stu-id="fb806-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="fb806-145">定期清除浏览器 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="fb806-146">但是，CSRF 漏洞在本质上是 web 应用的问题，而不是最终用户的问题。</span><span class="sxs-lookup"><span data-stu-id="fb806-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="fb806-147">身份验证基础知识</span><span class="sxs-lookup"><span data-stu-id="fb806-147">Authentication fundamentals</span></span>

<span data-ttu-id="fb806-148">基于 Cookie 的身份验证是一种常用的身份验证形式。</span><span class="sxs-lookup"><span data-stu-id="fb806-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="fb806-149">基于令牌的身份验证系统不断增长，尤其是对于单页面应用程序（Spa）。</span><span class="sxs-lookup"><span data-stu-id="fb806-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="fb806-150">基于 Cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="fb806-150">Cookie-based authentication</span></span>

<span data-ttu-id="fb806-151">用户使用其用户名和密码进行身份验证时，将颁发令牌，其中包含可用于身份验证和授权的身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="fb806-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="fb806-152">令牌存储为每个客户端发出的请求附带的 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="fb806-153">生成和验证此 cookie 由 Cookie 身份验证中间件来执行。</span><span class="sxs-lookup"><span data-stu-id="fb806-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="fb806-154">[中间件](xref:fundamentals/middleware/index)将用户主体序列化为加密的 cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="fb806-155">在后续请求中，中间件将验证 cookie，重新创建主体，并将主体分配给[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)的[用户](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)属性。</span><span class="sxs-lookup"><span data-stu-id="fb806-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="fb806-156">基于令牌的身份验证</span><span class="sxs-lookup"><span data-stu-id="fb806-156">Token-based authentication</span></span>

<span data-ttu-id="fb806-157">在对用户进行身份验证时，他们将颁发一个令牌（而不是防伪令牌）。</span><span class="sxs-lookup"><span data-stu-id="fb806-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="fb806-158">令牌包含[声明](/dotnet/framework/security/claims-based-identity-model)形式的用户信息或引用令牌，该令牌将应用指向应用中维护的用户状态。</span><span class="sxs-lookup"><span data-stu-id="fb806-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="fb806-159">当用户尝试访问要求身份验证的资源时，会将令牌发送到应用程序，并以持有者令牌的形式提供附加的授权标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="fb806-160">这使应用无状态。</span><span class="sxs-lookup"><span data-stu-id="fb806-160">This makes the app stateless.</span></span> <span data-ttu-id="fb806-161">在每个后续请求中，将在请求服务器端验证时传递该令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="fb806-162">此标记未*加密*;*编码*。</span><span class="sxs-lookup"><span data-stu-id="fb806-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="fb806-163">在服务器上，将解码令牌来访问其信息。</span><span class="sxs-lookup"><span data-stu-id="fb806-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="fb806-164">若要在后续请求中发送令牌，请将该令牌存储在浏览器的本地存储中。</span><span class="sxs-lookup"><span data-stu-id="fb806-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="fb806-165">如果令牌存储在浏览器的本地存储中，请不要担心 CSRF 漏洞。</span><span class="sxs-lookup"><span data-stu-id="fb806-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="fb806-166">如果标记存储在 cookie 中，则需要考虑 CSRF。</span><span class="sxs-lookup"><span data-stu-id="fb806-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="fb806-167">有关详细信息，请参阅 GitHub 问题[SPA 代码示例添加两个 cookie](https://github.com/dotnet/AspNetCore.Docs/issues/13369)。</span><span class="sxs-lookup"><span data-stu-id="fb806-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="fb806-168">在一个域中托管多个应用</span><span class="sxs-lookup"><span data-stu-id="fb806-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="fb806-169">共享宿主环境容易遭受会话劫持、登录 CSRF 和其他攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="fb806-170">尽管 `example1.contoso.net` 和 `example2.contoso.net` 是不同的主机，但该域下的主机之间存在隐式信任关系 `*.contoso.net` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="fb806-171">这种隐式信任关系允许可能不受信任的主机影响彼此的 cookie （控制 AJAX 请求的相同来源策略并不一定适用于 HTTP cookie）。</span><span class="sxs-lookup"><span data-stu-id="fb806-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="fb806-172">如果不共享域，则可以阻止利用同一域中托管的应用之间的受信任 cookie 的攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="fb806-173">当每个应用托管在其自己的域中时，没有要利用的隐式 cookie 信任关系。</span><span class="sxs-lookup"><span data-stu-id="fb806-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="fb806-174">ASP.NET Core 防伪配置</span><span class="sxs-lookup"><span data-stu-id="fb806-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="fb806-175">ASP.NET Core 使用[ASP.NET Core 数据保护](xref:security/data-protection/introduction)来实现防伪。</span><span class="sxs-lookup"><span data-stu-id="fb806-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="fb806-176">必须将数据保护堆栈配置为在服务器场中运行。</span><span class="sxs-lookup"><span data-stu-id="fb806-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="fb806-177">有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="fb806-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb806-178">当在中调用以下某个 Api 时，防伪中间件将添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器中 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fb806-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb806-179">当在中调用时，防伪中间件将添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 中`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="fb806-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="fb806-180">在 ASP.NET Core 2.0 或更高版本中， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)将防伪标记注入 HTML 窗体元素。</span><span class="sxs-lookup"><span data-stu-id="fb806-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="fb806-181">文件中的以下标记 Razor 会自动生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="fb806-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="fb806-182">同样，如果窗体的方法不获取，则默认情况下， [IHtmlHelper](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)将生成防伪标记。</span><span class="sxs-lookup"><span data-stu-id="fb806-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="fb806-183">当 `<form>` 标记包含 `method="post"` 属性并且满足以下任一条件时，会自动生成 HTML 窗体元素的防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="fb806-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="fb806-184">操作属性为空（ `action=""` ）。</span><span class="sxs-lookup"><span data-stu-id="fb806-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="fb806-185">操作属性（）未提供 `<form method="post">` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="fb806-186">可以禁用 HTML 窗体元素的自动生成防伪标记：</span><span class="sxs-lookup"><span data-stu-id="fb806-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="fb806-187">显式禁用属性为的防伪令牌 `asp-antiforgery` ：</span><span class="sxs-lookup"><span data-stu-id="fb806-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="fb806-188">Form 元素使用 Tag Helper [！ opt out 符号](xref:mvc/views/tag-helpers/intro#opt-out)选择退出标记帮助器：</span><span class="sxs-lookup"><span data-stu-id="fb806-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="fb806-189">`FormTagHelper`从视图中删除。</span><span class="sxs-lookup"><span data-stu-id="fb806-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="fb806-190">`FormTagHelper`可以通过将以下指令添加到视图中，从视图中删除 Razor ：</span><span class="sxs-lookup"><span data-stu-id="fb806-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="fb806-191">[ Razor 页面](xref:razor-pages/index)自动从 XSRF/CSRF 中保护。</span><span class="sxs-lookup"><span data-stu-id="fb806-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="fb806-192">有关详细信息，请参阅[XSRF/CSRF 和 Razor Pages](xref:razor-pages/index#xsrf)。</span><span class="sxs-lookup"><span data-stu-id="fb806-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="fb806-193">防御 CSRF 攻击的最常见方法是使用*同步器令牌模式*（STP）。</span><span class="sxs-lookup"><span data-stu-id="fb806-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="fb806-194">当用户请求包含窗体数据的页面时，将使用 STP：</span><span class="sxs-lookup"><span data-stu-id="fb806-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="fb806-195">服务器向客户端发送与当前用户的标识关联的令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="fb806-196">客户端将该令牌发送回服务器以进行验证。</span><span class="sxs-lookup"><span data-stu-id="fb806-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="fb806-197">如果服务器收到的令牌与经过身份验证的用户的标识不匹配，则会拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="fb806-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="fb806-198">该令牌是唯一的且不可预测的。</span><span class="sxs-lookup"><span data-stu-id="fb806-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="fb806-199">令牌还可用于确保对一系列请求进行正确排序（例如，确保的请求序列为： page 1 > 第2页 > 第3页）。</span><span class="sxs-lookup"><span data-stu-id="fb806-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="fb806-200">ASP.NET Core MVC 和页面模板中的所有表单都 Razor 生成防伪标记。</span><span class="sxs-lookup"><span data-stu-id="fb806-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="fb806-201">以下对视图示例将生成防伪令牌：</span><span class="sxs-lookup"><span data-stu-id="fb806-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="fb806-202">将防伪标记显式添加到 `<form>` 元素，而不将标记帮助程序与 HTML 帮助程序一起使用 [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) ：</span><span class="sxs-lookup"><span data-stu-id="fb806-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="fb806-203">在上述每种情况下，ASP.NET Core 添加如下所示的隐藏窗体字段：</span><span class="sxs-lookup"><span data-stu-id="fb806-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="fb806-204">ASP.NET Core 包括三个用于处理防伪令牌的[筛选器](xref:mvc/controllers/filters)：</span><span class="sxs-lookup"><span data-stu-id="fb806-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="fb806-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="fb806-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="fb806-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="fb806-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="fb806-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="fb806-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="fb806-208">防伪选项</span><span class="sxs-lookup"><span data-stu-id="fb806-208">Antiforgery options</span></span>

<span data-ttu-id="fb806-209">自定义[防伪选项](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fb806-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fb806-210">&dagger;`Cookie`使用[CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)类的属性设置防伪属性。</span><span class="sxs-lookup"><span data-stu-id="fb806-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="fb806-211">选项</span><span class="sxs-lookup"><span data-stu-id="fb806-211">Option</span></span> | <span data-ttu-id="fb806-212">说明</span><span class="sxs-lookup"><span data-stu-id="fb806-212">Description</span></span> |
| ---
<span data-ttu-id="fb806-213">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-214">'Blazor'</span></span>
- <span data-ttu-id="fb806-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-215">'Identity'</span></span>
- <span data-ttu-id="fb806-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-217">'Razor'</span></span>
- <span data-ttu-id="fb806-218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-218">'SignalR' uid:</span></span> 

<span data-ttu-id="fb806-219">--- |---标题：作者：说明： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-220">'Blazor'</span></span>
- <span data-ttu-id="fb806-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-221">'Identity'</span></span>
- <span data-ttu-id="fb806-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-223">'Razor'</span></span>
- <span data-ttu-id="fb806-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb806-225">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-226">'Blazor'</span></span>
- <span data-ttu-id="fb806-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-227">'Identity'</span></span>
- <span data-ttu-id="fb806-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-229">'Razor'</span></span>
- <span data-ttu-id="fb806-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb806-231">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-232">'Blazor'</span></span>
- <span data-ttu-id="fb806-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-233">'Identity'</span></span>
- <span data-ttu-id="fb806-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-235">'Razor'</span></span>
- <span data-ttu-id="fb806-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-236">'SignalR' uid:</span></span> 

<span data-ttu-id="fb806-237">------ | |[Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) |确定用于创建防伪 cookie 的设置。</span><span class="sxs-lookup"><span data-stu-id="fb806-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="fb806-238">| |[FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) |防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="fb806-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="fb806-239">| |[HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) |防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="fb806-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="fb806-240">如果 `null` 为，则系统仅考虑窗体数据。</span><span class="sxs-lookup"><span data-stu-id="fb806-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="fb806-241">| |[SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) |指定是否取消生成 `X-Frame-Options` 标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="fb806-242">默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="fb806-243">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fb806-243">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="fb806-244">选项</span><span class="sxs-lookup"><span data-stu-id="fb806-244">Option</span></span> | <span data-ttu-id="fb806-245">说明</span><span class="sxs-lookup"><span data-stu-id="fb806-245">Description</span></span> |
| ---
<span data-ttu-id="fb806-246">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-247">'Blazor'</span></span>
- <span data-ttu-id="fb806-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-248">'Identity'</span></span>
- <span data-ttu-id="fb806-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-250">'Razor'</span></span>
- <span data-ttu-id="fb806-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-251">'SignalR' uid:</span></span> 

<span data-ttu-id="fb806-252">--- |---标题：作者：说明： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-253">'Blazor'</span></span>
- <span data-ttu-id="fb806-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-254">'Identity'</span></span>
- <span data-ttu-id="fb806-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-256">'Razor'</span></span>
- <span data-ttu-id="fb806-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb806-258">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-259">'Blazor'</span></span>
- <span data-ttu-id="fb806-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-260">'Identity'</span></span>
- <span data-ttu-id="fb806-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-262">'Razor'</span></span>
- <span data-ttu-id="fb806-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb806-264">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="fb806-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb806-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb806-265">'Blazor'</span></span>
- <span data-ttu-id="fb806-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb806-266">'Identity'</span></span>
- <span data-ttu-id="fb806-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb806-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb806-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb806-268">'Razor'</span></span>
- <span data-ttu-id="fb806-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb806-269">'SignalR' uid:</span></span> 

<span data-ttu-id="fb806-270">------ | |[Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) |确定用于创建防伪 cookie 的设置。</span><span class="sxs-lookup"><span data-stu-id="fb806-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="fb806-271">| |[CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) |Cookie 的域。</span><span class="sxs-lookup"><span data-stu-id="fb806-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="fb806-272">默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="fb806-272">Defaults to `null`.</span></span> <span data-ttu-id="fb806-273">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="fb806-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="fb806-274">建议的替代项为 Cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="fb806-275">| |[CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) |Cookie 的名称。</span><span class="sxs-lookup"><span data-stu-id="fb806-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="fb806-276">如果未设置，系统将生成一个以[DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) （"。AspNetCore. 防伪. "）。</span><span class="sxs-lookup"><span data-stu-id="fb806-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="fb806-277">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="fb806-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="fb806-278">建议的替代项为 Cookie.Name。</span><span class="sxs-lookup"><span data-stu-id="fb806-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="fb806-279">| |[CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) |Cookie 上设置的路径。</span><span class="sxs-lookup"><span data-stu-id="fb806-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="fb806-280">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="fb806-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="fb806-281">建议的替代项为 Cookie。</span><span class="sxs-lookup"><span data-stu-id="fb806-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="fb806-282">| |[FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) |防伪系统用于在视图中呈现防伪标记的隐藏窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="fb806-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="fb806-283">| |[HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) |防伪系统使用的标头的名称。</span><span class="sxs-lookup"><span data-stu-id="fb806-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="fb806-284">如果 `null` 为，则系统仅考虑窗体数据。</span><span class="sxs-lookup"><span data-stu-id="fb806-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="fb806-285">| |[RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) |指定防伪系统是否需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="fb806-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="fb806-286">如果为 `true` ，则非 HTTPS 请求会失败。</span><span class="sxs-lookup"><span data-stu-id="fb806-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="fb806-287">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fb806-287">Defaults to `false`.</span></span> <span data-ttu-id="fb806-288">此属性已过时，并将在将来的版本中删除。</span><span class="sxs-lookup"><span data-stu-id="fb806-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="fb806-289">建议的替代项为设置 SecurePolicy。</span><span class="sxs-lookup"><span data-stu-id="fb806-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="fb806-290">| |[SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) |指定是否取消生成 `X-Frame-Options` 标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="fb806-291">默认情况下，会生成一个值为 "SAMEORIGIN" 的标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="fb806-292">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fb806-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="fb806-293">有关详细信息，请参阅[cookieauthenticationoptions.authenticationtype](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。</span><span class="sxs-lookup"><span data-stu-id="fb806-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="fb806-294">通过 IAntiforgery 配置防伪功能</span><span class="sxs-lookup"><span data-stu-id="fb806-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="fb806-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)提供用于配置防伪功能的 API。</span><span class="sxs-lookup"><span data-stu-id="fb806-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="fb806-296">`IAntiforgery`可以在类的方法中请求 `Configure` `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="fb806-297">下面的示例使用应用程序主页中的中间件来生成防伪标记，并将其作为 cookie 发送到响应中（使用本主题后面部分介绍的默认角度命名约定）：</span><span class="sxs-lookup"><span data-stu-id="fb806-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="fb806-298">需要防伪验证</span><span class="sxs-lookup"><span data-stu-id="fb806-298">Require antiforgery validation</span></span>

<span data-ttu-id="fb806-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)是一个可应用于单个操作、控制器或全局的操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="fb806-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="fb806-300">将阻止对应用了此筛选器的操作发出的请求，除非该请求包含有效的防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="fb806-301">`ValidateAntiForgeryToken`特性要求对其标记的操作方法的请求进行标记，包括 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="fb806-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="fb806-302">如果该 `ValidateAntiForgeryToken` 特性应用于应用控制器，则可以用特性重写它 `IgnoreAntiforgeryToken` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="fb806-303">ASP.NET Core 不支持添加防伪令牌来自动获取请求。</span><span class="sxs-lookup"><span data-stu-id="fb806-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="fb806-304">仅自动验证不安全 HTTP 方法的防伪令牌</span><span class="sxs-lookup"><span data-stu-id="fb806-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="fb806-305">ASP.NET Core 应用不生成安全 HTTP 方法（GET、HEAD、OPTIONS 和 TRACE）的防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="fb806-306">`ValidateAntiForgeryToken` `IgnoreAntiforgeryToken` 可以使用[AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性，而不是广泛应用该属性，然后使用特性重写它。</span><span class="sxs-lookup"><span data-stu-id="fb806-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="fb806-307">此属性与属性的工作方式相同 `ValidateAntiForgeryToken` ，不同之处在于，它不需要使用以下 HTTP 方法发出的请求的令牌：</span><span class="sxs-lookup"><span data-stu-id="fb806-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="fb806-308">GET</span><span class="sxs-lookup"><span data-stu-id="fb806-308">GET</span></span>
* <span data-ttu-id="fb806-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="fb806-309">HEAD</span></span>
* <span data-ttu-id="fb806-310">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="fb806-310">OPTIONS</span></span>
* <span data-ttu-id="fb806-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="fb806-311">TRACE</span></span>

<span data-ttu-id="fb806-312">我们建议将 `AutoValidateAntiforgeryToken` 广泛用于非 API 方案。</span><span class="sxs-lookup"><span data-stu-id="fb806-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="fb806-313">这可确保默认情况下保护后操作。</span><span class="sxs-lookup"><span data-stu-id="fb806-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="fb806-314">另一种方法是默认忽略防伪标记，除非 `ValidateAntiForgeryToken` 应用于各个操作方法。</span><span class="sxs-lookup"><span data-stu-id="fb806-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="fb806-315">在这种情况下，更有可能在此方案中，不受错误阻止的 POST 操作方法，使应用容易受到 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="fb806-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="fb806-316">所有文章都应发送防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="fb806-317">Api 没有用于发送令牌的非 cookie 部分的自动机制。</span><span class="sxs-lookup"><span data-stu-id="fb806-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="fb806-318">实现可能取决于客户端代码实现。</span><span class="sxs-lookup"><span data-stu-id="fb806-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="fb806-319">下面显示了一些示例：</span><span class="sxs-lookup"><span data-stu-id="fb806-319">Some examples are shown below:</span></span>

<span data-ttu-id="fb806-320">类级别的示例：</span><span class="sxs-lookup"><span data-stu-id="fb806-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="fb806-321">全局示例：</span><span class="sxs-lookup"><span data-stu-id="fb806-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb806-322">服务器.Addmvc 以（options => 选项。Filters。 Add （new AutoValidateAntiforgeryTokenAttribute （）））;</span><span class="sxs-lookup"><span data-stu-id="fb806-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="fb806-323">重写全局或控制器防伪属性</span><span class="sxs-lookup"><span data-stu-id="fb806-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="fb806-324">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)筛选器用于消除给定操作（或控制器）的防伪标记的需要。</span><span class="sxs-lookup"><span data-stu-id="fb806-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="fb806-325">应用此筛选器时，此筛选器会覆盖 `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` 在更高级别指定的筛选器和筛选器。</span><span class="sxs-lookup"><span data-stu-id="fb806-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="fb806-326">身份验证后刷新令牌</span><span class="sxs-lookup"><span data-stu-id="fb806-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="fb806-327">用户通过将用户重定向到 "视图" 或 "页面" 页进行身份验证后，应刷新标记 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fb806-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="fb806-328">JavaScript、AJAX 和 Spa</span><span class="sxs-lookup"><span data-stu-id="fb806-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="fb806-329">在传统的基于 HTML 的应用程序中，使用隐藏的窗体字段将防伪令牌传递给服务器。</span><span class="sxs-lookup"><span data-stu-id="fb806-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="fb806-330">在基于 JavaScript 的新式应用和 Spa 中，许多请求是以编程方式进行的。</span><span class="sxs-lookup"><span data-stu-id="fb806-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="fb806-331">这些 AJAX 请求可能使用其他技术（例如请求标头或 cookie）来发送令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="fb806-332">如果使用 cookie 来存储身份验证令牌，并在服务器上对 API 请求进行身份验证，则 CSRF 是一个潜在问题。</span><span class="sxs-lookup"><span data-stu-id="fb806-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="fb806-333">如果使用本地存储来存储令牌，可能会降低 CSRF 漏洞，因为本地存储中的值不会随每个请求自动发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="fb806-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="fb806-334">因此，使用本地存储在客户端上存储防伪令牌并将令牌作为请求标头进行发送是建议的方法。</span><span class="sxs-lookup"><span data-stu-id="fb806-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="fb806-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fb806-335">JavaScript</span></span>

<span data-ttu-id="fb806-336">将 JavaScript 用于视图，可以使用视图中的服务创建令牌。</span><span class="sxs-lookup"><span data-stu-id="fb806-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="fb806-337">将[AspNetCore 防伪 IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)服务插入到视图中，并调用[GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)：</span><span class="sxs-lookup"><span data-stu-id="fb806-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="fb806-338">此方法无需直接处理来自服务器的 cookie 设置，也无需从客户端读取它。</span><span class="sxs-lookup"><span data-stu-id="fb806-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="fb806-339">前面的示例使用 JavaScript 读取 AJAX POST 标头的隐藏字段值。</span><span class="sxs-lookup"><span data-stu-id="fb806-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="fb806-340">JavaScript 还可以访问 cookie 中的令牌，并使用 cookie 的内容创建带有令牌值的标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="fb806-341">假设脚本请求将令牌发送到名为的标头 `X-CSRF-TOKEN` ，请将防伪服务配置为查找 `X-CSRF-TOKEN` 标头：</span><span class="sxs-lookup"><span data-stu-id="fb806-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="fb806-342">下面的示例使用 JavaScript 通过适当的标头发出 AJAX 请求：</span><span class="sxs-lookup"><span data-stu-id="fb806-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="fb806-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="fb806-343">AngularJS</span></span>

<span data-ttu-id="fb806-344">AngularJS 使用约定来寻址 CSRF。</span><span class="sxs-lookup"><span data-stu-id="fb806-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="fb806-345">如果服务器发送的 cookie 的名称为，则在 `XSRF-TOKEN` `$http` 将请求发送到服务器时，AngularJS 服务会将 cookie 值添加到标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="fb806-346">此过程是自动进行的。</span><span class="sxs-lookup"><span data-stu-id="fb806-346">This process is automatic.</span></span> <span data-ttu-id="fb806-347">不需要在客户端中显式设置标头。</span><span class="sxs-lookup"><span data-stu-id="fb806-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="fb806-348">标头的名称为 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="fb806-349">服务器应检测此标头并验证其内容。</span><span class="sxs-lookup"><span data-stu-id="fb806-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="fb806-350">对于在应用程序启动时使用此约定的 ASP.NET Core API：</span><span class="sxs-lookup"><span data-stu-id="fb806-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="fb806-351">配置应用程序以在名为的 cookie 中提供标记 `XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="fb806-352">配置防伪服务以查找名为的标头 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="fb806-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="fb806-353">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fb806-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="fb806-354">扩展防伪</span><span class="sxs-lookup"><span data-stu-id="fb806-354">Extend antiforgery</span></span>

<span data-ttu-id="fb806-355">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)类型允许开发人员通过往返每个标记中的其他数据来扩展反 CSRF 系统的行为。</span><span class="sxs-lookup"><span data-stu-id="fb806-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="fb806-356">每次生成字段标记时都会调用[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)方法，并且返回值嵌入到生成的标记中。</span><span class="sxs-lookup"><span data-stu-id="fb806-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="fb806-357">在验证令牌时，实施者可以返回时间戳、nonce 或任何其他值，然后调用[ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)来验证此数据。</span><span class="sxs-lookup"><span data-stu-id="fb806-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="fb806-358">客户端的用户名已嵌入到生成的令牌中，因此不需要包含此信息。</span><span class="sxs-lookup"><span data-stu-id="fb806-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="fb806-359">如果令牌包含补充数据但未 `IAntiForgeryAdditionalDataProvider` 配置，则不会验证补充数据。</span><span class="sxs-lookup"><span data-stu-id="fb806-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb806-360">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb806-360">Additional resources</span></span>

* <span data-ttu-id="fb806-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) [打开 Web 应用程序安全项目](https://www.owasp.org/index.php/Main_Page)（OWASP）。</span><span class="sxs-lookup"><span data-stu-id="fb806-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
