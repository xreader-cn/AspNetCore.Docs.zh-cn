---
title: GRPC 中的身份验证和授权 ASP.NET Core
author: jamesnk
description: 了解如何在 gRPC 中使用身份验证和授权 ASP.NET Core。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/26/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 34f7f8a5a22159329b3d6c4524943434c460c7fb
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602423"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>GRPC 中的身份验证和授权 ASP.NET Core

按[James 牛顿-k](https://twitter.com/jamesnk)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>对调用 gRPC 服务的用户进行身份验证

gRPC 可以与[ASP.NET Core authentication](xref:security/authentication/identity)一起使用, 以将用户与每个调用相关联。

下面是一个示例`Startup.Configure` , 它使用 gRPC 和 ASP.NET Core authentication:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> 注册 ASP.NET Core 身份验证中间件的顺序。 始终调用`UseAuthentication`并`UseAuthorization`在`UseRouting`之后和`UseEndpoints`之前。

设置身份验证后, 可以通过`ServerCallContext`在 gRPC 服务方法中访问该用户。

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>持有者令牌身份验证

客户端可以提供用于身份验证的访问令牌。 服务器验证令牌并使用它来标识用户。

在服务器上，持有者令牌身份验证使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)进行配置。

在 .NET gRPC 客户端中, 可通过调用以标头的形式发送令牌:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

### <a name="client-certificate-authentication"></a>客户端证书身份验证

客户端还可以提供用于身份验证的客户端证书。 [证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)发生在 TLS 级别, 在它被 ASP.NET Core 之前。 当请求输入 ASP.NET Core 时,[客户端证书身份验证包](xref:security/authentication/certauth)可让你将证书解析为`ClaimsPrincipal`。

> [!NOTE]
> 需要将主机配置为接受客户端证书。 有关在 Kestrel、IIS 和 Azure 中接受客户端证书的信息, 请参阅[配置主机以要求提供证书](xref:security/authentication/certauth#configure-your-host-to-require-certificates)。

在 .net gRPC 客户端中, 会将客户端证书`HttpClientHandler`添加到, 然后使用该证书创建 gRPC 客户端:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a>其他身份验证机制

许多支持 ASP.NET Core 的身份验证机制都适用于 gRPC:

* Azure Active Directory
* 客户端证书
* IdentityServer
* JWT 令牌
* OAuth 2。0
* OpenID Connect
* WS-Federation

有关在服务器上配置身份验证的详细信息, 请参阅[ASP.NET Core authentication](xref:security/authentication/identity)。

将 gRPC 客户端配置为使用身份验证将取决于所使用的身份验证机制。 以前的持有者令牌和客户端证书示例显示了几种方法, gRPC 客户端可以配置为通过 gRPC 调用发送身份验证元数据:

* 强类型化 gRPC 客户`HttpClient`端内部使用。 可在上[`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)配置身份验证, 或者通过将[`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler)自定义实例`HttpClient`添加到来配置身份验证。
* 每个 gRPC 调用都有`CallOptions`一个可选参数。 可以使用选项的标头集合来发送自定义标头。

> [!NOTE]
> Windows 身份验证 (NTLM/Kerberos/协商) 不能与 gRPC 一起使用。 gRPC 要求 HTTP/2, 并且 HTTP/2 不支持 Windows 身份验证。

## <a name="authorize-users-to-access-services-and-service-methods"></a>授权用户访问服务和服务方法

默认情况下, 服务中的所有方法都可以由未经身份验证的用户调用。 若要要求身份验证, 请将[[授权]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)特性应用于服务:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

您可以使用该`[Authorize]`属性的构造函数参数和属性, 将访问权限限制为仅匹配特定[授权策略](xref:security/authorization/policies)的用户。 例如, 如果你有一个名`MyAuthorizationPolicy`为的自定义授权策略, 请确保只有符合该策略的用户才能使用以下代码访问该服务:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

单个服务方法还可以应用`[Authorize]`该属性。 如果当前用户与应用于方法和类的策略**都**不匹配, 则会向调用方返回错误:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core 中的持有者令牌身份验证](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [在 ASP.NET Core 中配置客户端证书身份验证](xref:security/authentication/certauth)
