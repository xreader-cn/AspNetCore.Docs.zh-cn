---
title: GRPC 中的身份验证和授权 ASP.NET Core
author: jamesnk
description: 了解如何在 gRPC 中使用身份验证和授权 ASP.NET Core。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 258b34113f3c3d9ef2031a43295ea5806b1e22ff
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880688"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="21ab8-103">GRPC 中的身份验证和授权 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21ab8-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="21ab8-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="21ab8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="21ab8-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="21ab8-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="21ab8-106">对调用 gRPC 服务的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="21ab8-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="21ab8-107">gRPC 可以与[ASP.NET Core authentication](xref:security/authentication/identity)一起使用，以将用户与每个调用相关联。</span><span class="sxs-lookup"><span data-stu-id="21ab8-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="21ab8-108">下面是使用 gRPC 和 ASP.NET Core 身份验证的 `Startup.Configure` 的示例：</span><span class="sxs-lookup"><span data-stu-id="21ab8-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="21ab8-109">注册 ASP.NET Core 身份验证中间件的顺序。</span><span class="sxs-lookup"><span data-stu-id="21ab8-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="21ab8-110">始终在 `UseRouting` 之后和 `UseEndpoints`之前调用 `UseAuthentication` 和 `UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="21ab8-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="21ab8-111">需要配置应用在调用期间使用的身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="21ab8-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="21ab8-112">身份验证配置是在 `Startup.ConfigureServices` 中添加的，会根据应用使用的身份验证机制而有所不同。</span><span class="sxs-lookup"><span data-stu-id="21ab8-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="21ab8-113">有关如何保护 ASP.NET Core 应用的示例，请参阅[身份验证示例](xref:security/authentication/samples)。</span><span class="sxs-lookup"><span data-stu-id="21ab8-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="21ab8-114">设置身份验证后，可以通过 `ServerCallContext`在 gRPC 服务方法中访问该用户。</span><span class="sxs-lookup"><span data-stu-id="21ab8-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="21ab8-115">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="21ab8-115">Bearer token authentication</span></span>

<span data-ttu-id="21ab8-116">客户端可以提供用于身份验证的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="21ab8-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="21ab8-117">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="21ab8-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="21ab8-118">在服务器上，持有者令牌身份验证使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)进行配置。</span><span class="sxs-lookup"><span data-stu-id="21ab8-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="21ab8-119">在 .NET gRPC 客户端中，可通过调用以标头的形式发送令牌：</span><span class="sxs-lookup"><span data-stu-id="21ab8-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="21ab8-120">在通道上配置 `ChannelCredentials` 是使用 gRPC 调用将令牌发送到服务的一种替代方法。</span><span class="sxs-lookup"><span data-stu-id="21ab8-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="21ab8-121">每次进行 gRPC 调用时，都将运行该凭据，这样就无需在多个位置编写代码即可自行传递令牌。</span><span class="sxs-lookup"><span data-stu-id="21ab8-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="21ab8-122">以下示例中的凭据将通道配置为通过每个 gRPC 调用发送令牌：</span><span class="sxs-lookup"><span data-stu-id="21ab8-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="21ab8-123">客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="21ab8-123">Client certificate authentication</span></span>

<span data-ttu-id="21ab8-124">客户端还可以提供用于身份验证的客户端证书。</span><span class="sxs-lookup"><span data-stu-id="21ab8-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="21ab8-125">[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)发生在 TLS 级别，在它被 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="21ab8-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="21ab8-126">当请求输入 ASP.NET Core 时，[客户端证书身份验证包](xref:security/authentication/certauth)可让你将证书解析为 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="21ab8-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="21ab8-127">需要将主机配置为接受客户端证书。</span><span class="sxs-lookup"><span data-stu-id="21ab8-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="21ab8-128">有关在 Kestrel、IIS 和 Azure 中接受客户端证书的信息，请参阅[配置主机以要求提供证书](xref:security/authentication/certauth#configure-your-host-to-require-certificates)。</span><span class="sxs-lookup"><span data-stu-id="21ab8-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="21ab8-129">在 .NET gRPC 客户端中，会将客户端证书添加到 `HttpClientHandler`，然后使用该证书创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="21ab8-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="21ab8-130">其他身份验证机制</span><span class="sxs-lookup"><span data-stu-id="21ab8-130">Other authentication mechanisms</span></span>

<span data-ttu-id="21ab8-131">许多支持 ASP.NET Core 的身份验证机制都适用于 gRPC：</span><span class="sxs-lookup"><span data-stu-id="21ab8-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="21ab8-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="21ab8-132">Azure Active Directory</span></span>
* <span data-ttu-id="21ab8-133">客户端证书</span><span class="sxs-lookup"><span data-stu-id="21ab8-133">Client Certificate</span></span>
* <span data-ttu-id="21ab8-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="21ab8-134">IdentityServer</span></span>
* <span data-ttu-id="21ab8-135">JWT 令牌</span><span class="sxs-lookup"><span data-stu-id="21ab8-135">JWT Token</span></span>
* <span data-ttu-id="21ab8-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="21ab8-136">OAuth 2.0</span></span>
* <span data-ttu-id="21ab8-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="21ab8-137">OpenID Connect</span></span>
* <span data-ttu-id="21ab8-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="21ab8-138">WS-Federation</span></span>

<span data-ttu-id="21ab8-139">有关在服务器上配置身份验证的详细信息，请参阅[ASP.NET Core authentication](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="21ab8-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="21ab8-140">将 gRPC 客户端配置为使用身份验证将取决于所使用的身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="21ab8-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="21ab8-141">以前的持有者令牌和客户端证书示例显示了几种方法，gRPC 客户端可以配置为通过 gRPC 调用发送身份验证元数据：</span><span class="sxs-lookup"><span data-stu-id="21ab8-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="21ab8-142">强类型化 gRPC 客户端在内部使用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="21ab8-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="21ab8-143">可以在[HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)上配置身份验证，或者通过将自定义[HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler)实例添加到 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="21ab8-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="21ab8-144">每个 gRPC 调用都有一个可选 `CallOptions` 参数。</span><span class="sxs-lookup"><span data-stu-id="21ab8-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="21ab8-145">可以使用选项的标头集合来发送自定义标头。</span><span class="sxs-lookup"><span data-stu-id="21ab8-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="21ab8-146">Windows 身份验证（NTLM/Kerberos/协商）不能与 gRPC 一起使用。</span><span class="sxs-lookup"><span data-stu-id="21ab8-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="21ab8-147">gRPC 要求使用 HTTP/2，但是 HTTP/2 不支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="21ab8-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="21ab8-148">授权用户访问服务和服务方法</span><span class="sxs-lookup"><span data-stu-id="21ab8-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="21ab8-149">默认情况下，服务中的所有方法都可以由未经身份验证的用户调用。</span><span class="sxs-lookup"><span data-stu-id="21ab8-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="21ab8-150">若要要求身份验证，请将[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)特性应用于服务：</span><span class="sxs-lookup"><span data-stu-id="21ab8-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="21ab8-151">可以使用 `[Authorize]` 特性的构造函数参数和属性，将访问权限限制为仅提供给符合特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="21ab8-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="21ab8-152">例如，如果你有一个名为 `MyAuthorizationPolicy`的自定义授权策略，请确保只有符合该策略的用户才能使用以下代码访问该服务：</span><span class="sxs-lookup"><span data-stu-id="21ab8-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="21ab8-153">单个服务方法还可以应用 `[Authorize]` 特性。</span><span class="sxs-lookup"><span data-stu-id="21ab8-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="21ab8-154">如果当前用户与应用于方法和类的策略**都**不匹配，则会向调用方返回错误：</span><span class="sxs-lookup"><span data-stu-id="21ab8-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="21ab8-155">其他资源</span><span class="sxs-lookup"><span data-stu-id="21ab8-155">Additional resources</span></span>

* [<span data-ttu-id="21ab8-156">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="21ab8-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="21ab8-157">在 ASP.NET Core 中配置客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="21ab8-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
