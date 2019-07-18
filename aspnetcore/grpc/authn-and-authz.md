---
title: GRPC 中的身份验证和授权 ASP.NET Core
author: jamesnk
description: 了解如何在 gRPC 中使用身份验证和授权 ASP.NET Core。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/07/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 49024295e4db7976924397bb24567d92d6298562
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308770"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="ca042-103">GRPC 中的身份验证和授权 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca042-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="ca042-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ca042-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ca042-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ca042-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="ca042-106">对调用 gRPC 服务的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="ca042-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="ca042-107">gRPC 可以与[ASP.NET Core authentication](xref:security/authentication/identity)一起使用, 以将用户与每个调用相关联。</span><span class="sxs-lookup"><span data-stu-id="ca042-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="ca042-108">下面是一个示例`Startup.Configure` , 它使用 gRPC 和 ASP.NET Core authentication:</span><span class="sxs-lookup"><span data-stu-id="ca042-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="ca042-109">注册 ASP.NET Core 身份验证中间件的顺序。</span><span class="sxs-lookup"><span data-stu-id="ca042-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="ca042-110">始终调用`UseAuthentication`并`UseAuthorization`在`UseRouting`之后和`UseEndpoints`之前。</span><span class="sxs-lookup"><span data-stu-id="ca042-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="ca042-111">设置身份验证后, 可以通过`ServerCallContext`在 gRPC 服务方法中访问该用户。</span><span class="sxs-lookup"><span data-stu-id="ca042-111">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="ca042-112">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="ca042-112">Bearer token authentication</span></span>

<span data-ttu-id="ca042-113">客户端可以提供用于身份验证的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ca042-113">The client can provide an access token for authentication.</span></span> <span data-ttu-id="ca042-114">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="ca042-114">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="ca042-115">在服务器上，持有者令牌身份验证使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)进行配置。</span><span class="sxs-lookup"><span data-stu-id="ca042-115">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="ca042-116">在 .NET gRPC 客户端中, 可通过调用以标头的形式发送令牌:</span><span class="sxs-lookup"><span data-stu-id="ca042-116">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="ca042-117">客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="ca042-117">Client certificate authentication</span></span>

<span data-ttu-id="ca042-118">客户端还可以提供用于身份验证的客户端证书。</span><span class="sxs-lookup"><span data-stu-id="ca042-118">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="ca042-119">[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)发生在 TLS 级别, 在它被 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="ca042-119">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="ca042-120">当请求输入 ASP.NET Core 时,[客户端证书身份验证包](xref:security/authentication/certauth)可让你将证书解析为`ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="ca042-120">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="ca042-121">需要将主机配置为接受客户端证书。</span><span class="sxs-lookup"><span data-stu-id="ca042-121">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="ca042-122">有关在 Kestrel、IIS 和 Azure 中接受客户端证书的信息, 请参阅[配置主机以要求提供证书](xref:security/authentication/certauth#configure-your-host-to-require-certificates)。</span><span class="sxs-lookup"><span data-stu-id="ca042-122">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="ca042-123">在 .net gRPC 客户端中, 会将客户端证书`HttpClientHandler`添加到, 然后使用该证书创建 gRPC 客户端:</span><span class="sxs-lookup"><span data-stu-id="ca042-123">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="ca042-124">其他身份验证机制</span><span class="sxs-lookup"><span data-stu-id="ca042-124">Other authentication mechanisms</span></span>

<span data-ttu-id="ca042-125">除持有者令牌和客户端证书身份验证外, 所有 ASP.NET Core 支持的身份验证机制 (如 OAuth、OpenID 和 Negotiate) 都应与 gRPC 配合使用。</span><span class="sxs-lookup"><span data-stu-id="ca042-125">In addition to bearer token and client certificate authentication, all ASP.NET Core supported authentication mechanisms such as OAuth, OpenID and Negotiate should work with gRPC.</span></span> <span data-ttu-id="ca042-126">若要详细了解如何在服务器端配置身份验证, 请访问[ASP.NET Core 身份验证](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="ca042-126">Visit [ASP.NET Core authentication](xref:security/authentication/identity) for more information for configuring authentication on the server side.</span></span>

<span data-ttu-id="ca042-127">客户端配置将取决于您所使用的身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="ca042-127">Client side configuration will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="ca042-128">以前的持有者令牌和客户端证书身份验证示例显示了几种方法, gRPC 客户端可以配置为通过 gRPC 调用发送身份验证元数据:</span><span class="sxs-lookup"><span data-stu-id="ca042-128">The previous bearer token and client certificate authentication examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="ca042-129">强类型化 gRPC 客户`HttpClient`端内部使用。</span><span class="sxs-lookup"><span data-stu-id="ca042-129">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="ca042-130">可在上[`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)配置身份验证, 或者通过将[`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler)自定义实例`HttpClient`添加到来配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="ca042-130">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="ca042-131">每个 gRPC 调用都有`CallOptions`一个可选参数。</span><span class="sxs-lookup"><span data-stu-id="ca042-131">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="ca042-132">可以使用选项的标头集合来发送自定义标头。</span><span class="sxs-lookup"><span data-stu-id="ca042-132">Custom headers can be sent using the option's headers collection.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="ca042-133">授权用户访问服务和服务方法</span><span class="sxs-lookup"><span data-stu-id="ca042-133">Authorize users to access services and service methods</span></span>

<span data-ttu-id="ca042-134">默认情况下, 服务中的所有方法都可以由未经身份验证的用户调用。</span><span class="sxs-lookup"><span data-stu-id="ca042-134">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="ca042-135">若要要求身份验证, 请将[[授权]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)特性应用于服务:</span><span class="sxs-lookup"><span data-stu-id="ca042-135">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="ca042-136">您可以使用该`[Authorize]`属性的构造函数参数和属性, 将访问权限限制为仅匹配特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="ca042-136">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="ca042-137">例如, 如果你有一个名`MyAuthorizationPolicy`为的自定义授权策略, 请确保只有符合该策略的用户才能使用以下代码访问该服务:</span><span class="sxs-lookup"><span data-stu-id="ca042-137">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="ca042-138">单个服务方法还可以应用`[Authorize]`该属性。</span><span class="sxs-lookup"><span data-stu-id="ca042-138">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="ca042-139">如果当前用户与应用于方法和类的策略**都**不匹配, 则会向调用方返回错误:</span><span class="sxs-lookup"><span data-stu-id="ca042-139">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ca042-140">其他资源</span><span class="sxs-lookup"><span data-stu-id="ca042-140">Additional resources</span></span>

* [<span data-ttu-id="ca042-141">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="ca042-141">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="ca042-142">在 ASP.NET Core 中配置客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="ca042-142">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
