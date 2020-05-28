---
<span data-ttu-id="f27d6-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f27d6-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f27d6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f27d6-102">'Blazor'</span></span>
- <span data-ttu-id="f27d6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f27d6-103">'Identity'</span></span>
- <span data-ttu-id="f27d6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f27d6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f27d6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f27d6-105">'Razor'</span></span>
- <span data-ttu-id="f27d6-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f27d6-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="f27d6-107">在 ASP.NET Core 中配置证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f27d6-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="f27d6-108">`Microsoft.AspNetCore.Authentication.Certificate`包含类似于 ASP.NET Core 的[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)的实现。</span><span class="sxs-lookup"><span data-stu-id="f27d6-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="f27d6-109">证书身份验证发生在 TLS 级别，在它被 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="f27d6-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="f27d6-110">更准确地说，这是验证证书的身份验证处理程序，然后向你提供可将该证书解析到的事件 `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="f27d6-111">将[主机配置](#configure-your-host-to-require-certificates)为使用证书进行身份验证，如 IIS、Kestrel、Azure Web 应用，或者其他任何所用的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="f27d6-112">代理和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f27d6-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="f27d6-113">证书身份验证是一种有状态方案，主要用于代理或负载均衡器不处理客户端和服务器之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f27d6-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="f27d6-114">如果使用代理或负载平衡器，则仅当代理或负载均衡器：</span><span class="sxs-lookup"><span data-stu-id="f27d6-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="f27d6-115">处理身份验证。</span><span class="sxs-lookup"><span data-stu-id="f27d6-115">Handles the authentication.</span></span>
* <span data-ttu-id="f27d6-116">将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。</span><span class="sxs-lookup"><span data-stu-id="f27d6-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="f27d6-117">使用代理和负载平衡器的环境中证书身份验证的替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。</span><span class="sxs-lookup"><span data-stu-id="f27d6-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="f27d6-118">入门</span><span class="sxs-lookup"><span data-stu-id="f27d6-118">Get started</span></span>

<span data-ttu-id="f27d6-119">获取并应用 HTTPS 证书，并将[主机配置](#configure-your-host-to-require-certificates)为需要证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="f27d6-120">在 web 应用中，添加对包的引用 `Microsoft.AspNetCore.Authentication.Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="f27d6-121">然后在 `Startup.ConfigureServices` 方法中， `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 使用你的选项调用，同时提供一个委托，用于对 `OnCertificateValidated` 随请求发送的客户端证书进行任何补充验证。</span><span class="sxs-lookup"><span data-stu-id="f27d6-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="f27d6-122">将该信息转换为 `ClaimsPrincipal` 并在属性上设置 `context.Principal` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="f27d6-123">如果身份验证失败，此处理程序将 `403 (Forbidden)` `401 (Unauthorized)` 像你所料，返回响应，而不是。</span><span class="sxs-lookup"><span data-stu-id="f27d6-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="f27d6-124">原因是，在初次 TLS 连接期间应进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f27d6-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="f27d6-125">当它到达处理程序时，它的时间太晚。</span><span class="sxs-lookup"><span data-stu-id="f27d6-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="f27d6-126">无法将连接从匿名连接升级到证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="f27d6-127">还会 `app.UseAuthentication();` 在方法中添加 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="f27d6-128">否则， `HttpContext.User` 将不会设置为 `ClaimsPrincipal` 从证书创建。</span><span class="sxs-lookup"><span data-stu-id="f27d6-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="f27d6-129">例如：</span><span class="sxs-lookup"><span data-stu-id="f27d6-129">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="f27d6-130">前面的示例演示了添加证书身份验证的默认方法。</span><span class="sxs-lookup"><span data-stu-id="f27d6-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="f27d6-131">处理程序使用通用证书属性构造用户主体。</span><span class="sxs-lookup"><span data-stu-id="f27d6-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="f27d6-132">配置证书验证</span><span class="sxs-lookup"><span data-stu-id="f27d6-132">Configure certificate validation</span></span>

<span data-ttu-id="f27d6-133">`CertificateAuthenticationOptions`处理程序具有一些内置的验证，这些验证是你应在证书上执行的最小验证。</span><span class="sxs-lookup"><span data-stu-id="f27d6-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="f27d6-134">默认情况下，将启用这些设置中的每一个。</span><span class="sxs-lookup"><span data-stu-id="f27d6-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="f27d6-135">AllowedCertificateTypes = 链式、SelfSigned 或 All （链式 |SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="f27d6-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="f27d6-136">默认值：30`CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="f27d6-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="f27d6-137">此检查将验证是否只允许使用适当的证书类型。</span><span class="sxs-lookup"><span data-stu-id="f27d6-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="f27d6-138">如果应用使用自签名证书，则需要将此选项设置为 `CertificateTypes.All` 或 `CertificateTypes.SelfSigned` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="f27d6-139">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="f27d6-139">ValidateCertificateUse</span></span>

<span data-ttu-id="f27d6-140">默认值：30`true`</span><span class="sxs-lookup"><span data-stu-id="f27d6-140">Default value: `true`</span></span>

<span data-ttu-id="f27d6-141">此检查将验证客户端提供的证书是否具有客户端身份验证扩展密钥使用（EKU），或者根本没有 Eku。</span><span class="sxs-lookup"><span data-stu-id="f27d6-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="f27d6-142">如规范所示，如果未指定 EKU，则所有 Eku 均视为有效。</span><span class="sxs-lookup"><span data-stu-id="f27d6-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="f27d6-143">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="f27d6-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="f27d6-144">默认值：30`true`</span><span class="sxs-lookup"><span data-stu-id="f27d6-144">Default value: `true`</span></span>

<span data-ttu-id="f27d6-145">此检查将验证证书是否在其有效期内。</span><span class="sxs-lookup"><span data-stu-id="f27d6-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="f27d6-146">对于每个请求，处理程序将确保在其在其当前会话期间提供的证书过期时，该证书是有效的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="f27d6-147">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="f27d6-147">RevocationFlag</span></span>

<span data-ttu-id="f27d6-148">默认值：30`X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="f27d6-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="f27d6-149">一个标志，该标志指定将检查链中的哪些证书进行吊销。</span><span class="sxs-lookup"><span data-stu-id="f27d6-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="f27d6-150">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="f27d6-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="f27d6-151">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="f27d6-151">RevocationMode</span></span>

<span data-ttu-id="f27d6-152">默认值：30`X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="f27d6-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="f27d6-153">指定如何执行吊销检查的标志。</span><span class="sxs-lookup"><span data-stu-id="f27d6-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="f27d6-154">指定联机检查可能会导致长时间延迟，同时与证书颁发机构联系。</span><span class="sxs-lookup"><span data-stu-id="f27d6-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="f27d6-155">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="f27d6-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="f27d6-156">我是否可以将我的应用程序配置为只需要特定路径上的证书？</span><span class="sxs-lookup"><span data-stu-id="f27d6-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="f27d6-157">这是不可能的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-157">This isn't possible.</span></span> <span data-ttu-id="f27d6-158">请记住，证书交换已完成 HTTPS 会话的启动，在该连接上收到第一个请求之前，服务器会完成此操作，因此无法基于任何请求字段进行作用域。</span><span class="sxs-lookup"><span data-stu-id="f27d6-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="f27d6-159">处理程序事件</span><span class="sxs-lookup"><span data-stu-id="f27d6-159">Handler events</span></span>

<span data-ttu-id="f27d6-160">处理程序有两个事件：</span><span class="sxs-lookup"><span data-stu-id="f27d6-160">The handler has two events:</span></span>

* <span data-ttu-id="f27d6-161">`OnAuthenticationFailed`：如果在身份验证过程中发生异常，则调用，并允许您做出反应。</span><span class="sxs-lookup"><span data-stu-id="f27d6-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="f27d6-162">`OnCertificateValidated`：在验证证书后调用，并已创建默认主体。</span><span class="sxs-lookup"><span data-stu-id="f27d6-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="f27d6-163">此事件允许你执行自己的验证并增加或替换主体。</span><span class="sxs-lookup"><span data-stu-id="f27d6-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="f27d6-164">例如：</span><span class="sxs-lookup"><span data-stu-id="f27d6-164">For examples include:</span></span>
  * <span data-ttu-id="f27d6-165">确定你的服务是否知道该证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="f27d6-166">构造自己的主体。</span><span class="sxs-lookup"><span data-stu-id="f27d6-166">Constructing your own principal.</span></span> <span data-ttu-id="f27d6-167">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="f27d6-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="f27d6-168">如果发现入站证书不符合额外的验证，请调用 `context.Fail("failure reason")` 失败原因。</span><span class="sxs-lookup"><span data-stu-id="f27d6-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="f27d6-169">对于实际功能，你可能需要调用在依赖关系注入中注册的服务，该服务连接到数据库或其他类型的用户存储。</span><span class="sxs-lookup"><span data-stu-id="f27d6-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="f27d6-170">使用传递到委托中的上下文访问你的服务。</span><span class="sxs-lookup"><span data-stu-id="f27d6-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="f27d6-171">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="f27d6-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="f27d6-172">从概念上讲，验证证书是一种授权问题。</span><span class="sxs-lookup"><span data-stu-id="f27d6-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="f27d6-173">例如，在授权策略中添加一个颁发者或指纹，而不是在中， `OnCertificateValidated` 这是完全可以接受的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="f27d6-174">将主机配置为需要证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="f27d6-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f27d6-175">Kestrel</span></span>

<span data-ttu-id="f27d6-176">在*Program.cs*中，按如下所示配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f27d6-176">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="f27d6-177">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="f27d6-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="f27d6-178">IIS</span><span class="sxs-lookup"><span data-stu-id="f27d6-178">IIS</span></span>

<span data-ttu-id="f27d6-179">在 IIS 管理器中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="f27d6-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="f27d6-180">从 "**连接**" 选项卡中选择你的站点。</span><span class="sxs-lookup"><span data-stu-id="f27d6-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="f27d6-181">双击 "**功能视图**" 窗口中的 " **SSL 设置**" 选项。</span><span class="sxs-lookup"><span data-stu-id="f27d6-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="f27d6-182">选中 "**需要 SSL** " 复选框，并选择 "**客户端证书**" 部分中的 "**要求**" 单选按钮。</span><span class="sxs-lookup"><span data-stu-id="f27d6-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="f27d6-184">Azure 和自定义 web 代理</span><span class="sxs-lookup"><span data-stu-id="f27d6-184">Azure and custom web proxies</span></span>

<span data-ttu-id="f27d6-185">有关如何配置证书转发中间件的详细[说明，请参阅托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)。</span><span class="sxs-lookup"><span data-stu-id="f27d6-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="f27d6-186">在 Azure Web 应用中使用证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f27d6-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="f27d6-187">Azure 不需要转发配置。</span><span class="sxs-lookup"><span data-stu-id="f27d6-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="f27d6-188">此设置已在证书转发中间件中进行设置。</span><span class="sxs-lookup"><span data-stu-id="f27d6-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="f27d6-189">这要求存在 CertificateForwardingMiddleware。</span><span class="sxs-lookup"><span data-stu-id="f27d6-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="f27d6-190">在自定义 web 代理中使用证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f27d6-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="f27d6-191">`AddCertificateForwarding`方法用于指定：</span><span class="sxs-lookup"><span data-stu-id="f27d6-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="f27d6-192">客户端标头名称。</span><span class="sxs-lookup"><span data-stu-id="f27d6-192">The client header name.</span></span>
* <span data-ttu-id="f27d6-193">如何加载证书（使用 `HeaderConverter` 属性）。</span><span class="sxs-lookup"><span data-stu-id="f27d6-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="f27d6-194">例如，在自定义 web 代理中，证书作为自定义请求标头传递 `X-SSL-CERT` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="f27d6-195">若要使用它，请在中配置证书转发 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f27d6-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="f27d6-196">然后，该 `Startup.Configure` 方法将添加中间件。</span><span class="sxs-lookup"><span data-stu-id="f27d6-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="f27d6-197">`UseCertificateForwarding`调用和之前调用 `UseAuthentication` `UseAuthorization` ：</span><span class="sxs-lookup"><span data-stu-id="f27d6-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="f27d6-198">单独的类可用于实现验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="f27d6-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="f27d6-199">由于本示例中使用了相同的自签名证书，因此请确保只能使用证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="f27d6-200">验证客户端证书和服务器证书的指纹是否匹配，否则，任何证书都可以使用，并且足以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f27d6-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="f27d6-201">这将在方法中使用 `AddCertificate` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="f27d6-202">如果使用的是中间证书或子证书，也可以在此处验证使用者或颁发者。</span><span class="sxs-lookup"><span data-stu-id="f27d6-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="f27d6-203">使用证书和 HttpClientHandler 实现 HttpClient</span><span class="sxs-lookup"><span data-stu-id="f27d6-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="f27d6-204">HttpClientHandler 可以直接添加到 HttpClient 类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="f27d6-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="f27d6-205">创建 HttpClient 的实例时应格外小心。</span><span class="sxs-lookup"><span data-stu-id="f27d6-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="f27d6-206">然后，HttpClient 将随每个请求发送证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-206">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="f27d6-207">使用证书和 IHttpClientFactory 中的命名 HttpClient 实现 HttpClient</span><span class="sxs-lookup"><span data-stu-id="f27d6-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="f27d6-208">在下面的示例中，使用处理程序中的 ClientCertificates 属性将客户端证书添加到 HttpClientHandler 中。</span><span class="sxs-lookup"><span data-stu-id="f27d6-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="f27d6-209">然后，可以使用 ConfigurePrimaryHttpMessageHandler 方法在 HttpClient 的命名实例中使用此处理程序。</span><span class="sxs-lookup"><span data-stu-id="f27d6-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="f27d6-210">这是在 ConfigureServices 方法中的 Startup 类中设置的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="f27d6-211">然后，可以使用 IHttpClientFactory 通过处理程序和证书获取命名实例。</span><span class="sxs-lookup"><span data-stu-id="f27d6-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="f27d6-212">使用 Startup 类中定义的客户端名称的 CreateClient 方法来获取实例。</span><span class="sxs-lookup"><span data-stu-id="f27d6-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="f27d6-213">可根据需要使用客户端发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="f27d6-213">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="f27d6-214">如果将正确的证书发送到服务器，则返回数据。</span><span class="sxs-lookup"><span data-stu-id="f27d6-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="f27d6-215">如果未发送证书或证书不正确，则返回 HTTP 403 状态代码。</span><span class="sxs-lookup"><span data-stu-id="f27d6-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="f27d6-216">在 PowerShell 中创建证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="f27d6-217">创建证书是最难设置此流的部分。</span><span class="sxs-lookup"><span data-stu-id="f27d6-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="f27d6-218">可以使用 PowerShell cmdlet 创建根证书 `New-SelfSignedCertificate` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="f27d6-219">创建证书时，请使用强密码。</span><span class="sxs-lookup"><span data-stu-id="f27d6-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="f27d6-220">`KeyUsageProperty`如图所示，添加参数和参数非常重要 `KeyUsage` 。</span><span class="sxs-lookup"><span data-stu-id="f27d6-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="f27d6-221">创建根 CA</span><span class="sxs-lookup"><span data-stu-id="f27d6-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="f27d6-222">`-DnsName`参数值必须与应用的部署目标匹配。</span><span class="sxs-lookup"><span data-stu-id="f27d6-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="f27d6-223">例如，用于开发的 "localhost"。</span><span class="sxs-lookup"><span data-stu-id="f27d6-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="f27d6-224">在受信任的根中安装</span><span class="sxs-lookup"><span data-stu-id="f27d6-224">Install in the trusted root</span></span>

<span data-ttu-id="f27d6-225">根证书需要在主机系统上受信任。</span><span class="sxs-lookup"><span data-stu-id="f27d6-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="f27d6-226">默认情况下，不信任证书颁发机构创建的根证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="f27d6-227">下面的链接说明了如何在 Windows 上完成此操作：</span><span class="sxs-lookup"><span data-stu-id="f27d6-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="f27d6-228">中间证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-228">Intermediate certificate</span></span>

<span data-ttu-id="f27d6-229">现在可以从根证书创建中间证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="f27d6-230">这并不是所有用例所必需的，但你可能需要创建多个证书，或者需要激活或禁用证书组。</span><span class="sxs-lookup"><span data-stu-id="f27d6-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="f27d6-231">`TextExtension`参数是设置证书的基本约束中的路径长度所必需的。</span><span class="sxs-lookup"><span data-stu-id="f27d6-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="f27d6-232">然后，可以将中间证书添加到 Windows 主机系统中的受信任中间证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="f27d6-233">从中间证书创建子证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="f27d6-234">可以从中间证书创建子证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="f27d6-235">这是最终实体，不需要创建更多的子证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="f27d6-236">从根证书创建子证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="f27d6-237">还可以直接从根证书创建子证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="f27d6-238">示例根中间证书证书</span><span class="sxs-lookup"><span data-stu-id="f27d6-238">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="f27d6-239">使用根证书、中间证书或子证书时，可以根据需要使用指纹或 PublicKey 来验证证书。</span><span class="sxs-lookup"><span data-stu-id="f27d6-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
