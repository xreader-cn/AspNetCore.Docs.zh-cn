---
title: 在 ASP.NET Core 中配置证书身份验证
author: blowdart
description: 了解如何在 ASP.NET Core for IIS 和 http.sys 中配置证书身份验证。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: ce7bcdbfb8ce0f1febf34b49786e92c917be139c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384839"
---
# <a name="overview"></a>概述

`Microsoft.AspNetCore.Authentication.Certificate`包含类似于 ASP.NET Core 的[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)的实现。 证书身份验证发生在 TLS 级别，在它被 ASP.NET Core 之前。 更准确地说，这是验证证书的身份验证处理程序，然后向你提供可将该证书解析到的`ClaimsPrincipal`事件。 

将[主机配置](#configure-your-host-to-require-certificates)为使用证书进行身份验证，如 IIS、Kestrel、Azure Web 应用，或者其他任何所用的。

## <a name="proxy-and-load-balancer-scenarios"></a>代理和负载均衡器方案

证书身份验证是一种有状态方案，主要用于代理或负载均衡器不处理客户端和服务器之间的流量。 如果使用代理或负载平衡器，则仅当代理或负载均衡器：

* 处理身份验证。
* 将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。

使用代理和负载平衡器的环境中证书身份验证的替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。

## <a name="get-started"></a>入门

获取并应用 HTTPS 证书，并将[主机配置](#configure-your-host-to-require-certificates)为需要证书。

在 web 应用中，添加对`Microsoft.AspNetCore.Authentication.Certificate`包的引用。 然后在`Startup.Configure`方法中，使用`app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`你的选项调用`OnCertificateValidated` ，同时提供一个委托，用于对随请求发送的客户端证书进行任何补充验证。 将该信息转换为`ClaimsPrincipal`并`context.Principal`在属性上设置。

如果身份验证失败，此处理程序`403 (Forbidden)`将像你`401 (Unauthorized)`所料，返回响应，而不是。 原因是，在初次 TLS 连接期间应进行身份验证。 当它到达处理程序时，它的时间太晚。 无法将连接从匿名连接升级到证书。

还会`app.UseAuthentication();` `Startup.Configure`在方法中添加。 否则，HttpContext 将不会设置为`ClaimsPrincipal`从证书创建。 例如:

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

前面的示例演示了添加证书身份验证的默认方法。 处理程序使用通用证书属性构造用户主体。

## <a name="configure-certificate-validation"></a>配置证书验证

`CertificateAuthenticationOptions`处理程序具有一些内置的验证，这些验证是你应在证书上执行的最小验证。 默认情况下，将启用这些设置中的每一个。

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = 链式、SelfSigned 或 All （链式 |SelfSigned)

此检查将验证是否只允许使用适当的证书类型。

### <a name="validatecertificateuse"></a>ValidateCertificateUse

此检查将验证客户端提供的证书是否具有客户端身份验证扩展密钥使用（EKU），或者根本没有 Eku。 如规范所示，如果未指定 EKU，则所有 Eku 均视为有效。

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

此检查将验证证书是否在其有效期内。 对于每个请求，处理程序将确保在其在其当前会话期间提供的证书过期时，该证书是有效的。

### <a name="revocationflag"></a>RevocationFlag

一个标志，该标志指定将检查链中的哪些证书进行吊销。

仅当证书链接到根证书时才执行吊销检查。

### <a name="revocationmode"></a>RevocationMode

指定如何执行吊销检查的标志。

指定联机检查可能会导致长时间延迟，同时与证书颁发机构联系。

仅当证书链接到根证书时才执行吊销检查。

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>我是否可以将我的应用程序配置为只需要特定路径上的证书？

这是不可能的。 请记住，证书交换已完成 HTTPS 会话的启动，在该连接上收到第一个请求之前，服务器会完成此操作，因此无法基于任何请求字段进行作用域。

## <a name="handler-events"></a>处理程序事件

处理程序有两个事件：

* `OnAuthenticationFailed`&ndash;如果在身份验证过程中发生异常，则调用，并允许您做出反应。
* `OnCertificateValidated`&ndash;在验证证书后调用，已经创建了验证并创建了一个默认主体。 此事件允许你执行自己的验证并增加或替换主体。 例如：
  * 确定你的服务是否知道该证书。
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

如果发现入站证书不符合额外的验证，请调用`context.Fail("failure reason")`失败原因。

对于实际功能，你可能需要调用在依赖关系注入中注册的服务，该服务连接到数据库或其他类型的用户存储。 使用传递到委托中的上下文访问你的服务。 请看下面 `Startup.ConfigureServices` 中的示例：

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

从概念上讲，验证证书是一种授权问题。 例如，在授权策略中添加一个颁发者或指纹，而不`OnCertificateValidated`是在中，这是完全可以接受的。

## <a name="configure-your-host-to-require-certificates"></a>将主机配置为需要证书

### <a name="kestrel"></a>Kestrel

在*Program.cs*中，按如下所示配置 Kestrel：

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

在 IIS 管理器中完成以下步骤：

1. 从 "**连接**" 选项卡中选择你的站点。
1. 双击 "**功能视图**" 窗口中的 " **SSL 设置**" 选项。
1. 选中 "**需要 SSL** " 复选框，并选择 "**客户端证书**" 部分中的 "**要求**" 单选按钮。

![IIS 中的客户端证书设置](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 和自定义 web 代理

有关如何配置证书转发中间件的详细[说明，请参阅托管和部署文档](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)。
