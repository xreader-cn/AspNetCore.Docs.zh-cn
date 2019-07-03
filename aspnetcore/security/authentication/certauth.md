---
title: 在 ASP.NET Core 中配置证书身份验证
author: blowdart
description: 了解如何为 IIS 和 HTTP.sys 在 ASP.NET Core 中配置证书身份验证。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538723"
---
# <a name="overview"></a><span data-ttu-id="6b0b9-103">概述</span><span class="sxs-lookup"><span data-stu-id="6b0b9-103">Overview</span></span>

<span data-ttu-id="6b0b9-104">`Microsoft.AspNetCore.Authentication.Certificate` 包含类似于实现[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)适用于 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="6b0b9-105">之前曾获取 ASP.NET Core，证书的身份验证发生在 TLS 级别，长时间。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="6b0b9-106">更准确地说，这是一个身份验证处理程序，用于验证证书并使您可以在其中解析到该证书事件`ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="6b0b9-107">[配置你的主机](#configure-your-host-to-require-certificates)对于证书身份验证，无论是 IIS、 Kestrel、 Azure Web 应用，或正在使用的其他任何内容。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="6b0b9-108">入门</span><span class="sxs-lookup"><span data-stu-id="6b0b9-108">Get started</span></span>

<span data-ttu-id="6b0b9-109">获取 HTTPS 证书、 应用它，并[配置主机](#configure-your-host-to-require-certificates)为需要证书。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="6b0b9-110">在 web 应用中，添加对的引用`Microsoft.AspNetCore.Authentication.Certificate`包。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="6b0b9-111">然后在`Startup.Configure`方法中，调用`app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`所需选项，提供了一个委托`OnCertificateValidated`能够在执行任何补充随请求一起发送的客户端证书验证。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="6b0b9-112">将这些信息转化`ClaimsPrincipal`并将其设置上`context.Principal`属性。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="6b0b9-113">如果身份验证失败，此处理程序返回`403 (Forbidden)`响应而不是`401 (Unauthorized)`，正如您所料。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="6b0b9-114">原因在于在初始的 TLS 连接，应执行的身份验证操作。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="6b0b9-115">达到该处理程序时，那就太晚。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="6b0b9-116">没有办法从匿名连接的连接升级到另一个使用证书。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="6b0b9-117">此外将添加`app.UseAuthentication();`在`Startup.Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="6b0b9-118">否则，HttpContext.User 将不设置为`ClaimsPrincipal`从证书创建。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="6b0b9-119">例如：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-119">For example:</span></span>

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

<span data-ttu-id="6b0b9-120">前面的示例演示如何添加证书的身份验证的默认方法。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="6b0b9-121">该处理程序构造使用常见的证书属性的用户主体。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="6b0b9-122">配置证书验证</span><span class="sxs-lookup"><span data-stu-id="6b0b9-122">Configure certificate validation</span></span>

<span data-ttu-id="6b0b9-123">`CertificateAuthenticationOptions`处理程序具有一些内置的验证的最小应执行对证书的验证。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="6b0b9-124">默认情况下启用的每个设置。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="6b0b9-125">AllowedCertificateTypes = 链接在一起，SelfSigned，或全部 (链接 |SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="6b0b9-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="6b0b9-126">此检查验证，允许仅将适当的证书类型。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="6b0b9-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="6b0b9-127">ValidateCertificateUse</span></span>

<span data-ttu-id="6b0b9-128">此检查验证客户端所出示的证书具有客户端身份验证扩展密钥使用 (EKU) 或任何 Eku 根本。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="6b0b9-129">正如规范所说，如果指定不使用任何 EKU，所有 Eku 将被都视为有效。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="6b0b9-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="6b0b9-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="6b0b9-131">此检查验证证书在其有效期内。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="6b0b9-132">每个请求，处理程序可确保在其当前会话期间尚未过期时提供了有效的证书。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="6b0b9-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="6b0b9-133">RevocationFlag</span></span>

<span data-ttu-id="6b0b9-134">一个标志，指定哪些证书链中检查已吊销。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="6b0b9-135">该证书链接到的根证书时，才会执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="6b0b9-136">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="6b0b9-136">RevocationMode</span></span>

<span data-ttu-id="6b0b9-137">一个标志，指定如何执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="6b0b9-138">指定在线检查可能导致长时间的延迟，而联系证书颁发机构。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="6b0b9-139">该证书链接到的根证书时，才会执行吊销检查。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="6b0b9-140">可以配置我的应用程序以要求仅在某些路径上的证书吗？</span><span class="sxs-lookup"><span data-stu-id="6b0b9-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="6b0b9-141">不可行。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-141">This isn't possible.</span></span> <span data-ttu-id="6b0b9-142">请记住，HTTPS 会话开始时，请为此服务器上该连接收到的第一个请求，因此不可能对基于任何请求的字段的作用域之前完成证书交换。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="6b0b9-143">处理程序的事件</span><span class="sxs-lookup"><span data-stu-id="6b0b9-143">Handler events</span></span>

<span data-ttu-id="6b0b9-144">该处理程序具有两个事件：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-144">The handler has two events:</span></span>

* <span data-ttu-id="6b0b9-145">`OnAuthenticationFailed` &ndash; 如果异常发生在身份验证过程并使你可以响应，调用。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="6b0b9-146">`OnCertificateValidated` &ndash; 证书通过验证后，已通过验证并已创建一个默认主体之后调用。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="6b0b9-147">此事件，可以执行自己的验证和增强或替换该主体。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="6b0b9-148">有关示例包括：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-148">For examples include:</span></span>
  * <span data-ttu-id="6b0b9-149">确定是否证书被认为您的服务。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="6b0b9-150">构造自己的主体。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-150">Constructing your own principal.</span></span> <span data-ttu-id="6b0b9-151">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="6b0b9-152">如果您发现的入站的证书不满足额外的验证，调用`context.Fail("failure reason")`含失败原因。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="6b0b9-153">为实际功能，您可能需要调用服务在连接到数据库或其他类型的用户存储的依赖关系注入中注册。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="6b0b9-154">通过使用传递到委托的上下文中访问你的服务。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="6b0b9-155">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="6b0b9-156">从概念上讲，证书验证的是，一个授权的问题。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="6b0b9-157">例如，添加检查、 颁发者或指纹的授权策略，而不是内部`OnCertificateValidated`，是完全可以接受。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="6b0b9-158">你将主机配置为需要证书</span><span class="sxs-lookup"><span data-stu-id="6b0b9-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="6b0b9-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="6b0b9-159">Kestrel</span></span>

<span data-ttu-id="6b0b9-160">在中*Program.cs*，将 Kestrel 配置，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-160">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a><span data-ttu-id="6b0b9-161">IIS</span><span class="sxs-lookup"><span data-stu-id="6b0b9-161">IIS</span></span>

<span data-ttu-id="6b0b9-162">完成以下步骤在 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="6b0b9-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="6b0b9-163">选择从你的站点**连接**选项卡。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="6b0b9-164">双击**SSL 设置**选项**功能视图**窗口。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="6b0b9-165">检查**要求 SSL**复选框，然后选择**需要**中的单选按钮**客户端证书**部分。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![在 IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="6b0b9-167">Azure 和自定义 web 代理</span><span class="sxs-lookup"><span data-stu-id="6b0b9-167">Azure and custom web proxies</span></span>

<span data-ttu-id="6b0b9-168">请参阅[托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)了解用于配置转发中间件的证书。</span><span class="sxs-lookup"><span data-stu-id="6b0b9-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
