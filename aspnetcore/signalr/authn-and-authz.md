---
title: ASP.NET Core 中的身份验证和授权 SignalR
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 3b7216bb064ba06a4c909016e1efd4242a64a7ad
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652014"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="149ea-103">ASP.NET Core 中的身份验证和授权 SignalR</span><span class="sxs-lookup"><span data-stu-id="149ea-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="149ea-104">作者： [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="149ea-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="149ea-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="149ea-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a><span data-ttu-id="149ea-106">对连接到 SignalR 集线器的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="149ea-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="149ea-107"> 可以与[ASP.NET Core authentication](xref:security/authentication/identity)一起使用，以将用户与每个连接相关联。</span><span class="sxs-lookup"><span data-stu-id="149ea-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="149ea-108">在中心中，可以从[HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)属性访问身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="149ea-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="149ea-109">身份验证允许中心对与用户关联的所有连接调用方法。</span><span class="sxs-lookup"><span data-stu-id="149ea-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="149ea-110">有关详细信息，请参阅[SignalR中的 "管理用户和组](xref:signalr/groups)"。</span><span class="sxs-lookup"><span data-stu-id="149ea-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="149ea-111">单个用户可能与多个链接相关联。</span><span class="sxs-lookup"><span data-stu-id="149ea-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="149ea-112">下面是使用 SignalR 和 ASP.NET Core 身份验证的 `Startup.Configure` 的示例：</span><span class="sxs-lookup"><span data-stu-id="149ea-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="149ea-113">注册 SignalR 和 ASP.NET Core 身份验证中间件的顺序。</span><span class="sxs-lookup"><span data-stu-id="149ea-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="149ea-114">在 `UseSignalR` 之前始终调用 `UseAuthentication`，以便 SignalR 在 `HttpContext`上有用户。</span><span class="sxs-lookup"><span data-stu-id="149ea-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="149ea-115">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="149ea-115">Cookie authentication</span></span>

<span data-ttu-id="149ea-116">在基于浏览器的应用程序中，cookie 身份验证允许现有用户凭据自动流向 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="149ea-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="149ea-117">使用浏览器客户端时，无需额外配置。</span><span class="sxs-lookup"><span data-stu-id="149ea-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="149ea-118">如果用户已登录到你的应用，则 SignalR 连接将自动继承此身份验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="149ea-119">Cookie 是一种特定于浏览器的发送访问令牌的方式，但非浏览器客户端也可以发送这些令牌。</span><span class="sxs-lookup"><span data-stu-id="149ea-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="149ea-120">使用[.Net 客户端](xref:signalr/dotnet-client)时，可以在 `.WithUrl` 调用中配置 `Cookies` 属性以提供 cookie。</span><span class="sxs-lookup"><span data-stu-id="149ea-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="149ea-121">但是，从 .NET 客户端使用 cookie 身份验证要求应用提供 API 来交换 cookie 的身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="149ea-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="149ea-122">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="149ea-122">Bearer token authentication</span></span>

<span data-ttu-id="149ea-123">客户端可以提供访问令牌，而不是使用 cookie。</span><span class="sxs-lookup"><span data-stu-id="149ea-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="149ea-124">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="149ea-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="149ea-125">仅在建立连接时才执行此验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="149ea-126">连接开启后，服务器不会通过自动重新验证来检查令牌是否撤销。</span><span class="sxs-lookup"><span data-stu-id="149ea-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="149ea-127">在服务器上，使用[JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)来配置持有者令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="149ea-128">在 JavaScript 客户端中，可使用[accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)选项提供令牌。</span><span class="sxs-lookup"><span data-stu-id="149ea-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="149ea-129">在 .NET 客户端中，有一个类似的[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)属性，可用于配置令牌：</span><span class="sxs-lookup"><span data-stu-id="149ea-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="149ea-130">提供的访问令牌函数在 SignalR发出的**每个**HTTP 请求之前调用。</span><span class="sxs-lookup"><span data-stu-id="149ea-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="149ea-131">如果你需要续订标记以便使连接保持活动状态（因为它可能会在连接期间过期），请在此函数中执行此操作，并返回已更新的令牌。</span><span class="sxs-lookup"><span data-stu-id="149ea-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="149ea-132">在标准 web Api 中，持有者令牌是在 HTTP 标头中发送的。</span><span class="sxs-lookup"><span data-stu-id="149ea-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="149ea-133">但是，在使用某些传输时，SignalR 无法在浏览器中设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="149ea-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="149ea-134">使用 Websocket 和服务器发送事件时，会将令牌作为查询字符串参数进行传输。</span><span class="sxs-lookup"><span data-stu-id="149ea-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="149ea-135">若要在服务器上支持此操作，需要进行其他配置：</span><span class="sxs-lookup"><span data-stu-id="149ea-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="149ea-136">由于浏览器 API 限制，连接到 Websocket 和服务器发送事件时，将在浏览器上使用查询字符串。</span><span class="sxs-lookup"><span data-stu-id="149ea-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="149ea-137">使用 HTTPS 时，查询字符串值受 TLS 连接保护。</span><span class="sxs-lookup"><span data-stu-id="149ea-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="149ea-138">但是，许多服务器都记录查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="149ea-138">However, many servers log query string values.</span></span> <span data-ttu-id="149ea-139">有关详细信息，请参阅[ASP.NET Core SignalR中的安全注意事项](xref:signalr/security)。</span><span class="sxs-lookup"><span data-stu-id="149ea-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="149ea-140"> 使用标头在支持令牌的环境（如 .NET 和 Java 客户端）中传输令牌。</span><span class="sxs-lookup"><span data-stu-id="149ea-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="149ea-141">Cookie 和持有者令牌</span><span class="sxs-lookup"><span data-stu-id="149ea-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="149ea-142">Cookie 特定于浏览器。</span><span class="sxs-lookup"><span data-stu-id="149ea-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="149ea-143">与发送持有者令牌相比，从其他类型的客户端发送这些客户端增加了复杂性。</span><span class="sxs-lookup"><span data-stu-id="149ea-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="149ea-144">因此，不建议使用 cookie 身份验证，除非应用只需从浏览器客户端对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="149ea-145">当使用浏览器客户端之外的客户端时，建议使用持有者令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="149ea-146">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="149ea-146">Windows authentication</span></span>

<span data-ttu-id="149ea-147">如果在你的应用中配置了[Windows 身份验证](xref:security/authentication/windowsauth)，SignalR 可以使用该标识来保护中心。</span><span class="sxs-lookup"><span data-stu-id="149ea-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="149ea-148">但是，若要将消息发送给单个用户，则需要添加自定义用户 ID 提供程序。</span><span class="sxs-lookup"><span data-stu-id="149ea-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="149ea-149">Windows 身份验证系统不提供 "名称标识符" 声明。</span><span class="sxs-lookup"><span data-stu-id="149ea-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="149ea-150"> 使用声明来确定用户名。</span><span class="sxs-lookup"><span data-stu-id="149ea-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="149ea-151">添加一个实现 `IUserIdProvider` 的新类，并从用户中检索一个声明以用作标识符。</span><span class="sxs-lookup"><span data-stu-id="149ea-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="149ea-152">例如，若要使用 "名称" 声明（`[Domain]\[Username]`格式的 Windows 用户名），请创建以下类：</span><span class="sxs-lookup"><span data-stu-id="149ea-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="149ea-153">你可以使用 `User` 中的任何值（例如 Windows SID 标识符，等等），而不是 `ClaimTypes.Name`。</span><span class="sxs-lookup"><span data-stu-id="149ea-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="149ea-154">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="149ea-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="149ea-155">否则，用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="149ea-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="149ea-156">在 `Startup.ConfigureServices` 方法中注册此组件。</span><span class="sxs-lookup"><span data-stu-id="149ea-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="149ea-157">在 .NET 客户端中，必须通过设置[UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)属性来启用 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="149ea-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="149ea-158">使用 Microsoft Internet Explorer 或 Microsoft Edge 时，浏览器客户端仅支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="149ea-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="149ea-159">使用声明自定义标识处理</span><span class="sxs-lookup"><span data-stu-id="149ea-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="149ea-160">对用户进行身份验证的应用可以从用户声明派生 SignalR 用户 Id。</span><span class="sxs-lookup"><span data-stu-id="149ea-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="149ea-161">若要指定 SignalR 如何创建用户 Id，请实现 `IUserIdProvider` 并注册实现。</span><span class="sxs-lookup"><span data-stu-id="149ea-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="149ea-162">示例代码演示了如何使用声明选择用户的电子邮件地址作为识别属性。</span><span class="sxs-lookup"><span data-stu-id="149ea-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="149ea-163">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="149ea-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="149ea-164">否则，用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="149ea-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="149ea-165">帐户注册将 `ClaimsTypes.Email` 类型的声明添加到 ASP.NET 标识数据库。</span><span class="sxs-lookup"><span data-stu-id="149ea-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="149ea-166">在 `Startup.ConfigureServices`中注册此组件。</span><span class="sxs-lookup"><span data-stu-id="149ea-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="149ea-167">授权用户访问集线器和集线器方法</span><span class="sxs-lookup"><span data-stu-id="149ea-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="149ea-168">默认情况下，集线器中的所有方法都可由未经身份验证的用户调用。</span><span class="sxs-lookup"><span data-stu-id="149ea-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="149ea-169">若要要求身份验证，请将[授权](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性应用于中心：</span><span class="sxs-lookup"><span data-stu-id="149ea-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="149ea-170">您可以使用 `[Authorize]` 属性的构造函数参数和属性，将访问权限限制为仅匹配特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="149ea-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="149ea-171">例如，如果你有一个名为的自定义授权策略 `MyAuthorizationPolicy` 你可以确保只有符合该策略的用户可以使用以下代码访问该中心：</span><span class="sxs-lookup"><span data-stu-id="149ea-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="149ea-172">单个集线器方法也可以应用 `[Authorize]` 特性。</span><span class="sxs-lookup"><span data-stu-id="149ea-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="149ea-173">如果当前用户与应用于方法的策略不匹配，则会向调用方返回错误：</span><span class="sxs-lookup"><span data-stu-id="149ea-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="149ea-174">使用授权处理程序自定义集线器方法授权</span><span class="sxs-lookup"><span data-stu-id="149ea-174">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="149ea-175">当集线器方法要求授权时，SignalR 向授权处理程序提供自定义资源。</span><span class="sxs-lookup"><span data-stu-id="149ea-175">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="149ea-176">资源是 `HubInvocationContext` 的一个实例。</span><span class="sxs-lookup"><span data-stu-id="149ea-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="149ea-177">`HubInvocationContext` 包括 `HubCallerContext`、正在调用的集线器方法的名称，以及中心方法的参数。</span><span class="sxs-lookup"><span data-stu-id="149ea-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="149ea-178">请考虑允许通过 Azure Active Directory 多个组织登录的聊天室的示例。</span><span class="sxs-lookup"><span data-stu-id="149ea-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="149ea-179">拥有 Microsoft 帐户的任何人都可以登录到聊天，但只有拥有组织的成员才能阻止用户或查看用户的聊天历史记录。</span><span class="sxs-lookup"><span data-stu-id="149ea-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="149ea-180">而且，我们可能希望限制某些用户的某些功能。</span><span class="sxs-lookup"><span data-stu-id="149ea-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="149ea-181">使用 ASP.NET Core 3.0 中的更新功能，这是完全可能的。</span><span class="sxs-lookup"><span data-stu-id="149ea-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="149ea-182">请注意 `DomainRestrictedRequirement` 如何作为自定义 `IAuthorizationRequirement`。</span><span class="sxs-lookup"><span data-stu-id="149ea-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="149ea-183">既然正在传入 `HubInvocationContext` 资源参数，内部逻辑就可以检查正在调用中心的上下文，并决定是否允许用户执行单个集线器方法。</span><span class="sxs-lookup"><span data-stu-id="149ea-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="149ea-184">在 `Startup.ConfigureServices`中，添加新策略，并提供自定义 `DomainRestrictedRequirement` 要求作为参数，以创建 `DomainRestricted` 策略。</span><span class="sxs-lookup"><span data-stu-id="149ea-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="149ea-185">在前面的示例中，`DomainRestrictedRequirement` 类既是 `IAuthorizationRequirement` 又是该要求的 `AuthorizationHandler`。</span><span class="sxs-lookup"><span data-stu-id="149ea-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="149ea-186">可以将这两个组件拆分为单独的类，以分隔问题。</span><span class="sxs-lookup"><span data-stu-id="149ea-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="149ea-187">该示例方法的优点是，无需在启动过程中注入 `AuthorizationHandler`，因为要求和处理程序是相同的。</span><span class="sxs-lookup"><span data-stu-id="149ea-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="149ea-188">其他资源</span><span class="sxs-lookup"><span data-stu-id="149ea-188">Additional resources</span></span>

* [<span data-ttu-id="149ea-189">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="149ea-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="149ea-190">基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="149ea-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
