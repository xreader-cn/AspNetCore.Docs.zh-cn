---
title: 在 ASP.NET Core 中配置证书身份验证
author: blowdart
description: 了解如何在 ASP.NET Core for IIS 和 HTTP.sys 中配置证书身份验证。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 06803ee57824bbfac5725763938abbb9db0e360a
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568842"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="aa1ea-103">在 ASP.NET Core 中配置证书身份验证</span><span class="sxs-lookup"><span data-stu-id="aa1ea-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="aa1ea-104">`Microsoft.AspNetCore.Authentication.Certificate`包含类似于 ASP.NET Core 的[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)的实现。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="aa1ea-105">证书身份验证在 TLS 级别发生，远在到达 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="aa1ea-106">更准确地说，这是验证证书的身份验证处理程序，然后向你提供可将该证书解析到的事件 `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="aa1ea-107">将[服务器配置](#configure-your-server-to-require-certificates)为使用证书进行身份验证，如 IIS、Kestrel、Azure Web 应用，或者使用任何其他方法。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="aa1ea-108">代理和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="aa1ea-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="aa1ea-109">证书身份验证是一种有状态方案，主要用于代理或负载均衡器不处理客户端和服务器之间的流量。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="aa1ea-110">如果使用代理或负载平衡器，则仅当代理或负载均衡器：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="aa1ea-111">处理身份验证。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-111">Handles the authentication.</span></span>
* <span data-ttu-id="aa1ea-112">将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="aa1ea-113">使用代理和负载平衡器的环境中证书身份验证的替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="aa1ea-114">入门</span><span class="sxs-lookup"><span data-stu-id="aa1ea-114">Get started</span></span>

<span data-ttu-id="aa1ea-115">获取并应用 HTTPS 证书，并将[服务器配置](#configure-your-server-to-require-certificates)为需要证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="aa1ea-116">在 web 应用中，添加对[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate)包的引用。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="aa1ea-117">然后在 `Startup.ConfigureServices` 方法中， `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 使用你的选项调用，同时提供一个委托，用于对 `OnCertificateValidated` 随请求发送的客户端证书进行任何补充验证。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="aa1ea-118">将该信息转换为 `ClaimsPrincipal` 并在属性上设置 `context.Principal` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="aa1ea-119">如果身份验证失败，此处理程序将 `403 (Forbidden)` `401 (Unauthorized)` 像你所料，返回响应，而不是。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="aa1ea-120">原因是，在初次 TLS 连接期间应进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="aa1ea-121">当它到达处理程序时，它的时间太晚。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="aa1ea-122">无法将连接从匿名连接升级到证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="aa1ea-123">还会 `app.UseAuthentication();` 在方法中添加 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="aa1ea-124">否则， `HttpContext.User` 将不会设置为 `ClaimsPrincipal` 从证书创建。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="aa1ea-125">例如：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-125">For example:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

<span data-ttu-id="aa1ea-126">前面的示例演示了添加证书身份验证的默认方法。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="aa1ea-127">处理程序使用通用证书属性构造用户主体。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="aa1ea-128">配置证书验证</span><span class="sxs-lookup"><span data-stu-id="aa1ea-128">Configure certificate validation</span></span>

<span data-ttu-id="aa1ea-129">`CertificateAuthenticationOptions`处理程序具有一些内置的验证，这些验证是你应在证书上执行的最小验证。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="aa1ea-130">默认情况下，将启用这些设置中的每一个。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="aa1ea-131">AllowedCertificateTypes = 链式、SelfSigned 或 All （链式 |SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="aa1ea-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="aa1ea-132">默认值：30`CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="aa1ea-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="aa1ea-133">此检查将验证是否只允许使用适当的证书类型。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="aa1ea-134">如果应用使用自签名证书，则需要将此选项设置为 `CertificateTypes.All` 或 `CertificateTypes.SelfSigned` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="aa1ea-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="aa1ea-135">ValidateCertificateUse</span></span>

<span data-ttu-id="aa1ea-136">默认值：30`true`</span><span class="sxs-lookup"><span data-stu-id="aa1ea-136">Default value: `true`</span></span>

<span data-ttu-id="aa1ea-137">此检查将验证客户端提供的证书是否具有客户端身份验证扩展密钥使用（EKU），或者根本没有 Eku。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="aa1ea-138">如规范所示，如果未指定 EKU，则所有 Eku 均视为有效。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="aa1ea-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="aa1ea-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="aa1ea-140">默认值：30`true`</span><span class="sxs-lookup"><span data-stu-id="aa1ea-140">Default value: `true`</span></span>

<span data-ttu-id="aa1ea-141">此检查将验证证书是否在其有效期内。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="aa1ea-142">对于每个请求，处理程序将确保在其在其当前会话期间提供的证书过期时，该证书是有效的。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="aa1ea-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="aa1ea-143">RevocationFlag</span></span>

<span data-ttu-id="aa1ea-144">默认值：30`X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="aa1ea-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="aa1ea-145">一个标志，该标志指定将检查链中的哪些证书进行吊销。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="aa1ea-146">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="aa1ea-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="aa1ea-147">RevocationMode</span></span>

<span data-ttu-id="aa1ea-148">默认值：30`X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="aa1ea-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="aa1ea-149">指定如何执行吊销检查的标志。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="aa1ea-150">指定联机检查可能会导致长时间延迟，同时与证书颁发机构联系。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="aa1ea-151">仅当证书链接到根证书时才执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="aa1ea-152">我是否可以将我的应用程序配置为只需要特定路径上的证书？</span><span class="sxs-lookup"><span data-stu-id="aa1ea-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="aa1ea-153">这是不可能的。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-153">This isn't possible.</span></span> <span data-ttu-id="aa1ea-154">请记住，证书交换已完成 HTTPS 会话的启动，在该连接上收到第一个请求之前，服务器会完成此操作，因此无法基于任何请求字段进行作用域。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="aa1ea-155">处理程序事件</span><span class="sxs-lookup"><span data-stu-id="aa1ea-155">Handler events</span></span>

<span data-ttu-id="aa1ea-156">处理程序有两个事件：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-156">The handler has two events:</span></span>

* <span data-ttu-id="aa1ea-157">`OnAuthenticationFailed`：如果在身份验证过程中发生异常，则调用，并允许您做出反应。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="aa1ea-158">`OnCertificateValidated`：在验证证书后调用，并已创建默认主体。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="aa1ea-159">此事件允许你执行自己的验证并增加或替换主体。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="aa1ea-160">例如：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-160">For examples include:</span></span>
  * <span data-ttu-id="aa1ea-161">确定你的服务是否知道该证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="aa1ea-162">构造自己的主体。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-162">Constructing your own principal.</span></span> <span data-ttu-id="aa1ea-163">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="aa1ea-164">如果发现入站证书不符合额外的验证，请调用 `context.Fail("failure reason")` 失败原因。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="aa1ea-165">对于实际功能，你可能需要调用在依赖关系注入中注册的服务，该服务连接到数据库或其他类型的用户存储。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="aa1ea-166">使用传递到委托中的上下文访问你的服务。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="aa1ea-167">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="aa1ea-168">从概念上讲，验证证书是一种授权问题。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="aa1ea-169">例如，在授权策略中添加一个颁发者或指纹，而不是在中， `OnCertificateValidated` 这是完全可以接受的。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="aa1ea-170">将服务器配置为需要证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="aa1ea-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="aa1ea-171">Kestrel</span></span>

<span data-ttu-id="aa1ea-172">在*Program.cs*中，按如下所示配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-172">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="aa1ea-173">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="aa1ea-174">IIS</span><span class="sxs-lookup"><span data-stu-id="aa1ea-174">IIS</span></span>

<span data-ttu-id="aa1ea-175">在 IIS 管理器中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="aa1ea-176">从 "**连接**" 选项卡中选择你的站点。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="aa1ea-177">双击 "**功能视图**" 窗口中的 " **SSL 设置**" 选项。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="aa1ea-178">选中 "**需要 SSL** " 复选框，并选择 "**客户端证书**" 部分中的 "**要求**" 单选按钮。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="aa1ea-180">Azure 和自定义 web 代理</span><span class="sxs-lookup"><span data-stu-id="aa1ea-180">Azure and custom web proxies</span></span>

<span data-ttu-id="aa1ea-181">有关如何配置证书转发中间件的详细[说明，请参阅托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="aa1ea-182">在 Azure Web 应用中使用证书身份验证</span><span class="sxs-lookup"><span data-stu-id="aa1ea-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="aa1ea-183">Azure 不需要转发配置。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="aa1ea-184">此设置已在证书转发中间件中进行设置。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="aa1ea-185">这要求存在 CertificateForwardingMiddleware。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="aa1ea-186">在自定义 web 代理中使用证书身份验证</span><span class="sxs-lookup"><span data-stu-id="aa1ea-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="aa1ea-187">`AddCertificateForwarding`方法用于指定：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="aa1ea-188">客户端标头名称。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-188">The client header name.</span></span>
* <span data-ttu-id="aa1ea-189">如何加载证书（使用 `HeaderConverter` 属性）。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="aa1ea-190">例如，在自定义 web 代理中，证书作为自定义请求标头传递 `X-SSL-CERT` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="aa1ea-191">若要使用它，请在中配置证书转发 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="aa1ea-192">然后，该 `Startup.Configure` 方法将添加中间件。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="aa1ea-193">`UseCertificateForwarding`调用和之前调用 `UseAuthentication` `UseAuthorization` ：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="aa1ea-194">单独的类可用于实现验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="aa1ea-195">由于本示例中使用了相同的自签名证书，因此请确保只能使用证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="aa1ea-196">验证客户端证书和服务器证书的指纹是否匹配，否则，任何证书都可以使用，并且足以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="aa1ea-197">这将在方法中使用 `AddCertificate` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="aa1ea-198">如果使用的是中间证书或子证书，也可以在此处验证使用者或颁发者。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="aa1ea-199">使用证书和 HttpClientHandler 实现 HttpClient</span><span class="sxs-lookup"><span data-stu-id="aa1ea-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="aa1ea-200">`HttpClientHandler`可以直接在类的构造函数中添加 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="aa1ea-201">创建实例时应小心谨慎 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="aa1ea-202">`HttpClient`然后，将随每个请求发送证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-202">The `HttpClient` will then send the certificate with each request.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="aa1ea-203">使用证书和 IHttpClientFactory 中的命名 HttpClient 实现 HttpClient</span><span class="sxs-lookup"><span data-stu-id="aa1ea-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="aa1ea-204">在下面的示例中， `HttpClientHandler` 使用来自处理程序的属性将客户端证书添加到 `ClientCertificates` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="aa1ea-205">然后，可以使用方法在的命名实例中使用此处理程序 `HttpClient` `ConfigurePrimaryHttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="aa1ea-206">这是在中设置的 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-206">This is setup in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="aa1ea-207">`IHttpClientFactory`然后，可以使用处理程序和证书获取命名实例。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="aa1ea-208">使用 `CreateClient` 在类中定义的客户端名称的方法 `Startup` 来获取实例。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="aa1ea-209">可根据需要使用客户端发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-209">The HTTP request can be sent using the client as required.</span></span>

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

<span data-ttu-id="aa1ea-210">如果将正确的证书发送到服务器，则返回数据。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="aa1ea-211">如果未发送证书或证书不正确，则返回 HTTP 403 状态代码。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="aa1ea-212">在 PowerShell 中创建证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="aa1ea-213">创建证书是最难设置此流的部分。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="aa1ea-214">可以使用 PowerShell cmdlet 创建根证书 `New-SelfSignedCertificate` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="aa1ea-215">创建证书时，请使用强密码。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="aa1ea-216">`KeyUsageProperty`如图所示，添加参数和参数非常重要 `KeyUsage` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="aa1ea-217">创建根 CA</span><span class="sxs-lookup"><span data-stu-id="aa1ea-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="aa1ea-218">`-DnsName`参数值必须与应用的部署目标匹配。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="aa1ea-219">例如，用于开发的 "localhost"。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="aa1ea-220">在受信任的根中安装</span><span class="sxs-lookup"><span data-stu-id="aa1ea-220">Install in the trusted root</span></span>

<span data-ttu-id="aa1ea-221">根证书需要在主机系统上受信任。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="aa1ea-222">默认情况下，不信任证书颁发机构创建的根证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="aa1ea-223">下面的链接说明了如何在 Windows 上完成此操作：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="aa1ea-224">中间证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-224">Intermediate certificate</span></span>

<span data-ttu-id="aa1ea-225">现在可以从根证书创建中间证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="aa1ea-226">这并不是所有用例所必需的，但你可能需要创建多个证书，或者需要激活或禁用证书组。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="aa1ea-227">`TextExtension`参数是设置证书的基本约束中的路径长度所必需的。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="aa1ea-228">然后，可以将中间证书添加到 Windows 主机系统中的受信任中间证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="aa1ea-229">从中间证书创建子证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="aa1ea-230">可以从中间证书创建子证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="aa1ea-231">这是最终实体，不需要创建更多的子证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="aa1ea-232">从根证书创建子证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="aa1ea-233">还可以直接从根证书创建子证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="aa1ea-234">示例根中间证书证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-234">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="aa1ea-235">使用根证书、中间证书或子证书时，可以根据需要使用指纹或 PublicKey 来验证证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a><span data-ttu-id="aa1ea-236">证书验证缓存</span><span class="sxs-lookup"><span data-stu-id="aa1ea-236">Certificate validation caching</span></span>

<span data-ttu-id="aa1ea-237">ASP.NET Core 5.0 及更高版本支持启用验证结果缓存的功能。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="aa1ea-238">缓存极大地提高了证书身份验证的性能，因为验证是一种代价高昂的操作。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="aa1ea-239">默认情况下，证书身份验证禁用缓存。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="aa1ea-240">若要启用缓存，请调用 `AddCertificateCache` 中的 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

<span data-ttu-id="aa1ea-241">默认的缓存实现将结果存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="aa1ea-242">可以通过实现 `ICertificateValidationCache` 并将其注册到依赖关系注入来提供自己的缓存。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="aa1ea-243">例如，`services.AddSingleton<ICertificateValidationCache, YourCache>()`。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="aa1ea-244">可选客户端证书</span><span class="sxs-lookup"><span data-stu-id="aa1ea-244">Optional client certificates</span></span>

<span data-ttu-id="aa1ea-245">本部分为必须使用证书保护应用程序子集的应用提供信息。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="aa1ea-246">例如， Razor 应用中的页面或控制器可能需要客户端证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="aa1ea-247">这是客户端证书带来的挑战：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="aa1ea-248">是 TLS 功能，而不是 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="aa1ea-249">按连接进行协商，在连接开始时必须在任何 HTTP 数据可用之前进行协商。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="aa1ea-250">在连接开始时，仅知道服务器名称指示（SNI） &dagger; 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="aa1ea-251">客户端和服务器证书在第一次请求连接之前进行协商，请求通常无法重新协商。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="aa1ea-252">TLS 重新协商是实现可选客户端证书的一种方法。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="aa1ea-253">不建议这样做，因为：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="aa1ea-254">在 HTTP/1.1 中，在 POST 请求期间 renegotiating 可能会导致死锁，其中，请求正文填充 TCP 窗口，而重新协商数据包无法接收。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="aa1ea-255">HTTP/2[显式禁止](https://tools.ietf.org/html/rfc7540#section-9.2.1)重新协商。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="aa1ea-256">TLS 1.3 已[删除](https://tools.ietf.org/html/rfc8740#section-1)对重新协商的支持。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="aa1ea-257">ASP.NET Core 5 preview 7 及更高版本为可选的客户端证书添加了更方便的支持。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="aa1ea-258">有关详细信息，请参阅[可选证书示例](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample)。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="aa1ea-259">以下方法支持可选的客户端证书：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-259">The following approach supports optional client certificates:</span></span>

* <span data-ttu-id="aa1ea-260">设置域和子域的绑定：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="aa1ea-261">例如，在和上设置绑定 `contoso.com` `myClient.contoso.com` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="aa1ea-262">`contoso.com`主机不需要客户端证书，而是 `myClient.contoso.com` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="aa1ea-263">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-263">For more information, see:</span></span>
    * <span data-ttu-id="aa1ea-264">[Kestrel](/fundamentals/servers/kestrel)：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="aa1ea-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="aa1ea-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="aa1ea-266">请注意，Kestrel 目前不支持在一个绑定上支持多个 TLS 配置，需要两个具有唯一 Ip 或端口的绑定。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="aa1ea-267">请参阅https://github.com/dotnet/runtime/issues/31097</span><span class="sxs-lookup"><span data-stu-id="aa1ea-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="aa1ea-268">IIS</span><span class="sxs-lookup"><span data-stu-id="aa1ea-268">IIS</span></span>
      * [<span data-ttu-id="aa1ea-269">承载 IIS</span><span class="sxs-lookup"><span data-stu-id="aa1ea-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="aa1ea-270">配置 IIS 上的安全性</span><span class="sxs-lookup"><span data-stu-id="aa1ea-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="aa1ea-271">Http.Sys：[配置 Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="aa1ea-271">Http.Sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>
* <span data-ttu-id="aa1ea-272">对于需要客户端证书且没有客户端证书的 web 应用的请求：</span><span class="sxs-lookup"><span data-stu-id="aa1ea-272">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="aa1ea-273">使用客户端证书保护的子域重定向到同一页面。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-273">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="aa1ea-274">例如，重定向到 `myClient.contoso.com/requestedPage` 。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-274">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="aa1ea-275">由于与的请求 `myClient.contoso.com/requestedPage` 不同于的主机名 `contoso.com/requestedPage` ，因此客户端会建立一个不同的连接，并提供客户端证书。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-275">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="aa1ea-276">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-276">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="aa1ea-277">在[此 GitHub 讨论](https://github.com/dotnet/AspNetCore.Docs/issues/18720)问题中，对可选客户端证书留下疑问、评论和其他反馈。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-277">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="aa1ea-278">&dagger;服务器名称指示（SNI）是一种 TLS 扩展，可将虚拟域作为 SSL 协商的一部分包括在内。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-278">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="aa1ea-279">这实际上意味着，可以使用虚拟域名或主机名来识别网络终结点。</span><span class="sxs-lookup"><span data-stu-id="aa1ea-279">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
