---
title: 在 ASP.NET Core 中配置证书身份验证
author: blowdart
description: 了解如何在 ASP.NET Core for IIS 和 http.sys 中配置证书身份验证。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 11/05/2019
uid: security/authentication/certauth
ms.openlocfilehash: 081935e6e6248b5fe9b7bf4cd966dc73761d2ec1
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634053"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="f6c17-103">在 ASP.NET Core 中配置证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f6c17-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="f6c17-104">`Microsoft.AspNetCore.Authentication.Certificate` 包含类似于 ASP.NET Core[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)的实现。</span><span class="sxs-lookup"><span data-stu-id="f6c17-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="f6c17-105">证书身份验证发生在 TLS 级别，在它被 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="f6c17-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="f6c17-106">更准确地说，这是验证证书的身份验证处理程序，然后向你提供可将该证书解析到 `ClaimsPrincipal`的事件。</span><span class="sxs-lookup"><span data-stu-id="f6c17-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="f6c17-107">将[主机配置](#configure-your-host-to-require-certificates)为使用证书进行身份验证，如 IIS、Kestrel、Azure Web 应用，或者其他任何所用的。</span><span class="sxs-lookup"><span data-stu-id="f6c17-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="f6c17-108">代理和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f6c17-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="f6c17-109">证书身份验证是一种有状态方案，主要用于代理或负载均衡器不处理客户端和服务器之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f6c17-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="f6c17-110">如果使用代理或负载平衡器，则仅当代理或负载均衡器：</span><span class="sxs-lookup"><span data-stu-id="f6c17-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="f6c17-111">处理身份验证。</span><span class="sxs-lookup"><span data-stu-id="f6c17-111">Handles the authentication.</span></span>
* <span data-ttu-id="f6c17-112">将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。</span><span class="sxs-lookup"><span data-stu-id="f6c17-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="f6c17-113">使用代理和负载平衡器的环境中证书身份验证的替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。</span><span class="sxs-lookup"><span data-stu-id="f6c17-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="f6c17-114">入门</span><span class="sxs-lookup"><span data-stu-id="f6c17-114">Get started</span></span>

<span data-ttu-id="f6c17-115">获取并应用 HTTPS 证书，并将[主机配置](#configure-your-host-to-require-certificates)为需要证书。</span><span class="sxs-lookup"><span data-stu-id="f6c17-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="f6c17-116">在 web 应用中，添加对 `Microsoft.AspNetCore.Authentication.Certificate` 包的引用。</span><span class="sxs-lookup"><span data-stu-id="f6c17-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="f6c17-117">然后，在 `Startup.ConfigureServices` 方法中，使用你的选项调用 `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);`，并为 `OnCertificateValidated` 提供一个委托，以便在与请求一起发送的客户端证书上执行任何补充验证。</span><span class="sxs-lookup"><span data-stu-id="f6c17-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="f6c17-118">将该信息转换为 `ClaimsPrincipal`，并在 `context.Principal` 属性上对其进行设置。</span><span class="sxs-lookup"><span data-stu-id="f6c17-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="f6c17-119">如果身份验证失败，则此处理程序将返回 `403 (Forbidden)` 响应，而不是 `401 (Unauthorized)`，如你所料。</span><span class="sxs-lookup"><span data-stu-id="f6c17-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="f6c17-120">原因是，在初次 TLS 连接期间应进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f6c17-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="f6c17-121">当它到达处理程序时，它的时间太晚。</span><span class="sxs-lookup"><span data-stu-id="f6c17-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="f6c17-122">无法将连接从匿名连接升级到证书。</span><span class="sxs-lookup"><span data-stu-id="f6c17-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="f6c17-123">此外，还可以在 `Startup.Configure` 方法中添加 `app.UseAuthentication();`。</span><span class="sxs-lookup"><span data-stu-id="f6c17-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="f6c17-124">否则，HttpContext 不会设置为从证书创建 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="f6c17-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="f6c17-125">例如:</span><span class="sxs-lookup"><span data-stu-id="f6c17-125">For example:</span></span>

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

<span data-ttu-id="f6c17-126">前面的示例演示了添加证书身份验证的默认方法。</span><span class="sxs-lookup"><span data-stu-id="f6c17-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="f6c17-127">处理程序使用通用证书属性构造用户主体。</span><span class="sxs-lookup"><span data-stu-id="f6c17-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="f6c17-128">配置证书验证</span><span class="sxs-lookup"><span data-stu-id="f6c17-128">Configure certificate validation</span></span>

<span data-ttu-id="f6c17-129">`CertificateAuthenticationOptions` 处理程序具有一些内置的验证，这些验证是你应在证书上执行的最小验证。</span><span class="sxs-lookup"><span data-stu-id="f6c17-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="f6c17-130">默认情况下，将启用这些设置中的每一个。</span><span class="sxs-lookup"><span data-stu-id="f6c17-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="f6c17-131">AllowedCertificateTypes = 链式、SelfSigned 或 All （链式 |SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="f6c17-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="f6c17-132">此检查将验证是否只允许使用适当的证书类型。</span><span class="sxs-lookup"><span data-stu-id="f6c17-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="f6c17-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="f6c17-133">ValidateCertificateUse</span></span>

<span data-ttu-id="f6c17-134">此检查将验证客户端提供的证书是否具有客户端身份验证扩展密钥使用（EKU），或者根本没有 Eku。</span><span class="sxs-lookup"><span data-stu-id="f6c17-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="f6c17-135">如规范所示，如果未指定 EKU，则所有 Eku 均视为有效。</span><span class="sxs-lookup"><span data-stu-id="f6c17-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="f6c17-136">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="f6c17-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="f6c17-137">此检查将验证证书是否在其有效期内。</span><span class="sxs-lookup"><span data-stu-id="f6c17-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="f6c17-138">对于每个请求，处理程序将确保在其在其当前会话期间提供的证书过期时，该证书是有效的。</span><span class="sxs-lookup"><span data-stu-id="f6c17-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="f6c17-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="f6c17-139">RevocationFlag</span></span>

<span data-ttu-id="f6c17-140">一个标志，该标志指定将检查链中的哪些证书进行吊销。</span><span class="sxs-lookup"><span data-stu-id="f6c17-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="f6c17-141">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="f6c17-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="f6c17-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="f6c17-142">RevocationMode</span></span>

<span data-ttu-id="f6c17-143">指定如何执行吊销检查的标志。</span><span class="sxs-lookup"><span data-stu-id="f6c17-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="f6c17-144">指定联机检查可能会导致长时间延迟，同时与证书颁发机构联系。</span><span class="sxs-lookup"><span data-stu-id="f6c17-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="f6c17-145">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="f6c17-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="f6c17-146">我是否可以将我的应用程序配置为只需要特定路径上的证书？</span><span class="sxs-lookup"><span data-stu-id="f6c17-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="f6c17-147">这是不可能的。</span><span class="sxs-lookup"><span data-stu-id="f6c17-147">This isn't possible.</span></span> <span data-ttu-id="f6c17-148">请记住，证书交换已完成 HTTPS 会话的启动，在该连接上收到第一个请求之前，服务器会完成此操作，因此无法基于任何请求字段进行作用域。</span><span class="sxs-lookup"><span data-stu-id="f6c17-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="f6c17-149">处理程序事件</span><span class="sxs-lookup"><span data-stu-id="f6c17-149">Handler events</span></span>

<span data-ttu-id="f6c17-150">处理程序有两个事件：</span><span class="sxs-lookup"><span data-stu-id="f6c17-150">The handler has two events:</span></span>

* <span data-ttu-id="f6c17-151">如果在身份验证过程中发生异常，则调用 `OnAuthenticationFailed` &ndash;，并允许您做出反应。</span><span class="sxs-lookup"><span data-stu-id="f6c17-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="f6c17-152">验证证书后调用 `OnCertificateValidated` &ndash;，已创建验证，已创建默认主体。</span><span class="sxs-lookup"><span data-stu-id="f6c17-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="f6c17-153">此事件允许你执行自己的验证并增加或替换主体。</span><span class="sxs-lookup"><span data-stu-id="f6c17-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="f6c17-154">例如：</span><span class="sxs-lookup"><span data-stu-id="f6c17-154">For examples include:</span></span>
  * <span data-ttu-id="f6c17-155">确定你的服务是否知道该证书。</span><span class="sxs-lookup"><span data-stu-id="f6c17-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="f6c17-156">构造自己的主体。</span><span class="sxs-lookup"><span data-stu-id="f6c17-156">Constructing your own principal.</span></span> <span data-ttu-id="f6c17-157">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="f6c17-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f6c17-158">如果发现入站证书不能满足额外的验证，请调用 `context.Fail("failure reason")` 失败原因。</span><span class="sxs-lookup"><span data-stu-id="f6c17-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="f6c17-159">对于实际功能，你可能需要调用在依赖关系注入中注册的服务，该服务连接到数据库或其他类型的用户存储。</span><span class="sxs-lookup"><span data-stu-id="f6c17-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="f6c17-160">使用传递到委托中的上下文访问你的服务。</span><span class="sxs-lookup"><span data-stu-id="f6c17-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="f6c17-161">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="f6c17-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f6c17-162">从概念上讲，验证证书是一种授权问题。</span><span class="sxs-lookup"><span data-stu-id="f6c17-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="f6c17-163">例如，在授权策略中添加一个颁发者或指纹（而不是 `OnCertificateValidated`）是完全可以接受的。</span><span class="sxs-lookup"><span data-stu-id="f6c17-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="f6c17-164">将主机配置为需要证书</span><span class="sxs-lookup"><span data-stu-id="f6c17-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="f6c17-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f6c17-165">Kestrel</span></span>

<span data-ttu-id="f6c17-166">在*Program.cs*中，按如下所示配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f6c17-166">In *Program.cs*, configure Kestrel as follows:</span></span>

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
                        o.ConfigureHttpsDefaults(o => o.ClientCertificateMode = ClientCertificateMode.RequireCertificate);
                    });
                });
}
```

### <a name="iis"></a><span data-ttu-id="f6c17-167">IIS</span><span class="sxs-lookup"><span data-stu-id="f6c17-167">IIS</span></span>

<span data-ttu-id="f6c17-168">在 IIS 管理器中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="f6c17-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="f6c17-169">从 "**连接**" 选项卡中选择你的站点。</span><span class="sxs-lookup"><span data-stu-id="f6c17-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="f6c17-170">双击 "**功能视图**" 窗口中的 " **SSL 设置**" 选项。</span><span class="sxs-lookup"><span data-stu-id="f6c17-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="f6c17-171">选中 "**需要 SSL** " 复选框，并选择 "**客户端证书**" 部分中的 "**要求**" 单选按钮。</span><span class="sxs-lookup"><span data-stu-id="f6c17-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="f6c17-173">Azure 和自定义 web 代理</span><span class="sxs-lookup"><span data-stu-id="f6c17-173">Azure and custom web proxies</span></span>

<span data-ttu-id="f6c17-174">有关如何配置证书转发中间件的详细[说明，请参阅托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)。</span><span class="sxs-lookup"><span data-stu-id="f6c17-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
