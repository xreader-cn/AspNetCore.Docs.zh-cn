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
# <a name="overview"></a>概述

`Microsoft.AspNetCore.Authentication.Certificate` 包含类似于实现[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)适用于 ASP.NET Core。 之前曾获取 ASP.NET Core，证书的身份验证发生在 TLS 级别，长时间。 更准确地说，这是一个身份验证处理程序，用于验证证书并使您可以在其中解析到该证书事件`ClaimsPrincipal`。 

[配置你的主机](#configure-your-host-to-require-certificates)对于证书身份验证，无论是 IIS、 Kestrel、 Azure Web 应用，或正在使用的其他任何内容。

## <a name="get-started"></a>入门

获取 HTTPS 证书、 应用它，并[配置主机](#configure-your-host-to-require-certificates)为需要证书。

在 web 应用中，添加对的引用`Microsoft.AspNetCore.Authentication.Certificate`包。 然后在`Startup.Configure`方法中，调用`app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`所需选项，提供了一个委托`OnCertificateValidated`能够在执行任何补充随请求一起发送的客户端证书验证。 将这些信息转化`ClaimsPrincipal`并将其设置上`context.Principal`属性。

如果身份验证失败，此处理程序返回`403 (Forbidden)`响应而不是`401 (Unauthorized)`，正如您所料。 原因在于在初始的 TLS 连接，应执行的身份验证操作。 达到该处理程序时，那就太晚。 没有办法从匿名连接的连接升级到另一个使用证书。

此外将添加`app.UseAuthentication();`在`Startup.Configure`方法。 否则，HttpContext.User 将不设置为`ClaimsPrincipal`从证书创建。 例如：

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

前面的示例演示如何添加证书的身份验证的默认方法。 该处理程序构造使用常见的证书属性的用户主体。

## <a name="configure-certificate-validation"></a>配置证书验证

`CertificateAuthenticationOptions`处理程序具有一些内置的验证的最小应执行对证书的验证。 默认情况下启用的每个设置。

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = 链接在一起，SelfSigned，或全部 (链接 |SelfSigned)

此检查验证，允许仅将适当的证书类型。

### <a name="validatecertificateuse"></a>ValidateCertificateUse

此检查验证客户端所出示的证书具有客户端身份验证扩展密钥使用 (EKU) 或任何 Eku 根本。 正如规范所说，如果指定不使用任何 EKU，所有 Eku 将被都视为有效。

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

此检查验证证书在其有效期内。 每个请求，处理程序可确保在其当前会话期间尚未过期时提供了有效的证书。

### <a name="revocationflag"></a>RevocationFlag

一个标志，指定哪些证书链中检查已吊销。

该证书链接到的根证书时，才会执行吊销检查。

### <a name="revocationmode"></a>RevocationMode

一个标志，指定如何执行吊销检查。

指定在线检查可能导致长时间的延迟，而联系证书颁发机构。

该证书链接到的根证书时，才会执行吊销检查。

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>可以配置我的应用程序以要求仅在某些路径上的证书吗？

不可行。 请记住，HTTPS 会话开始时，请为此服务器上该连接收到的第一个请求，因此不可能对基于任何请求的字段的作用域之前完成证书交换。

## <a name="handler-events"></a>处理程序的事件

该处理程序具有两个事件：

* `OnAuthenticationFailed` &ndash; 如果异常发生在身份验证过程并使你可以响应，调用。
* `OnCertificateValidated` &ndash; 证书通过验证后，已通过验证并已创建一个默认主体之后调用。 此事件，可以执行自己的验证和增强或替换该主体。 有关示例包括：
  * 确定是否证书被认为您的服务。
  * 构造自己的主体。 请看下面 `Startup.ConfigureServices` 中的示例：

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

如果您发现的入站的证书不满足额外的验证，调用`context.Fail("failure reason")`含失败原因。

为实际功能，您可能需要调用服务在连接到数据库或其他类型的用户存储的依赖关系注入中注册。 通过使用传递到委托的上下文中访问你的服务。 请看下面 `Startup.ConfigureServices` 中的示例：

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

从概念上讲，证书验证的是，一个授权的问题。 例如，添加检查、 颁发者或指纹的授权策略，而不是内部`OnCertificateValidated`，是完全可以接受。

## <a name="configure-your-host-to-require-certificates"></a>你将主机配置为需要证书

### <a name="kestrel"></a>Kestrel

在中*Program.cs*，将 Kestrel 配置，如下所示：

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

### <a name="iis"></a>IIS

完成以下步骤在 IIS 管理器：

1. 选择从你的站点**连接**选项卡。
1. 双击**SSL 设置**选项**功能视图**窗口。
1. 检查**要求 SSL**复选框，然后选择**需要**中的单选按钮**客户端证书**部分。

![在 IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 和自定义 web 代理

请参阅[托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)了解用于配置转发中间件的证书。
