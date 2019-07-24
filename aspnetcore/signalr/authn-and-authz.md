---
title: ASP.NET Core SignalR 中的身份验证和授权
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR 中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 07/15/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e7e7a9fd537ba89b64c15594652a290357a00038
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412542"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="59e40-103">ASP.NET Core SignalR 中的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="59e40-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="59e40-104">作者: [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="59e40-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="59e40-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="59e40-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="59e40-106">对连接到 SignalR 中心的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="59e40-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="59e40-107">可将 SignalR 与 [ASP.NET Core 身份验证](xref:security/authentication/identity) 结合使用，将用户与每个连接相关联。</span><span class="sxs-lookup"><span data-stu-id="59e40-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="59e40-108">在中心，可以从 [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)属性访问身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="59e40-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="59e40-109">中心可借助身份验证在所有与用户关联的连接上调用方法（请参阅[在 SignalR 中管理用户和组](xref:signalr/groups)，了解相关详细信息）。</span><span class="sxs-lookup"><span data-stu-id="59e40-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="59e40-110">单个用户可能与多个链接相关联。</span><span class="sxs-lookup"><span data-stu-id="59e40-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="59e40-111">下面是一个示例`Startup.Configure` , 它使用 SignalR 和 ASP.NET Core authentication:</span><span class="sxs-lookup"><span data-stu-id="59e40-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="59e40-112">注册 SignalR 和 ASP.NET Core 身份验证中间件的顺序。</span><span class="sxs-lookup"><span data-stu-id="59e40-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="59e40-113">始终调用`UseAuthentication` `HttpContext`, `UseSignalR`以便 SignalR 在上有用户。</span><span class="sxs-lookup"><span data-stu-id="59e40-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="59e40-114">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="59e40-114">Cookie authentication</span></span>

<span data-ttu-id="59e40-115">在基于浏览器的应用中，凭借 cookie 身份验证，现有用户凭据可以自动传递到 SignalR 连接 。</span><span class="sxs-lookup"><span data-stu-id="59e40-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="59e40-116">使用浏览器客户端时，无需额外配置。</span><span class="sxs-lookup"><span data-stu-id="59e40-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="59e40-117">如果用户登录到你的应用，SignalR 连接便会自动继承此身份验证。</span><span class="sxs-lookup"><span data-stu-id="59e40-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="59e40-118">Cookie 是一种特定于浏览器的发送访问令牌的方式，但非浏览器客户端也可以发送这些令牌。</span><span class="sxs-lookup"><span data-stu-id="59e40-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="59e40-119">使用 [.NET 客户端](xref:signalr/dotnet-client) 时，可以在 `.WithUrl` 调用中配置 `Cookies` 属性来提供 cookie。</span><span class="sxs-lookup"><span data-stu-id="59e40-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="59e40-120">但是，使用 .NET 客户端的 cookie 身份验证时，应用需要提供 API 来交换 cookie 的身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="59e40-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="59e40-121">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="59e40-121">Bearer token authentication</span></span>

<span data-ttu-id="59e40-122">客户端可以提供访问令牌, 而不是使用 cookie。</span><span class="sxs-lookup"><span data-stu-id="59e40-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="59e40-123">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="59e40-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="59e40-124">仅在建立连接时才执行此验证。</span><span class="sxs-lookup"><span data-stu-id="59e40-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="59e40-125">连接开启后，服务器不会通过自动重新验证来检查令牌是否撤销。</span><span class="sxs-lookup"><span data-stu-id="59e40-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="59e40-126">在服务器上，持有者令牌身份验证使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)进行配置。</span><span class="sxs-lookup"><span data-stu-id="59e40-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="59e40-127">在 JavaScript 客户端中, 可使用[accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)选项提供令牌。</span><span class="sxs-lookup"><span data-stu-id="59e40-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="59e40-128">在 .NET 客户端中, 有一个类似的[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)属性可用于配置令牌:</span><span class="sxs-lookup"><span data-stu-id="59e40-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="59e40-129">提供的访问令牌函数在 SignalR 发出的**每个**HTTP 请求之前调用。</span><span class="sxs-lookup"><span data-stu-id="59e40-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="59e40-130">如果你需要续订标记以便使连接保持活动状态 (因为它可能会在连接期间过期), 请在此函数中执行此操作, 并返回已更新的令牌。</span><span class="sxs-lookup"><span data-stu-id="59e40-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="59e40-131">在标准 web Api 中, 持有者令牌是在 HTTP 标头中发送的。</span><span class="sxs-lookup"><span data-stu-id="59e40-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="59e40-132">但是, 在使用某些传输时, SignalR 无法在浏览器中设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="59e40-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="59e40-133">使用 Websocket 和服务器发送事件时, 会将令牌作为查询字符串参数进行传输。</span><span class="sxs-lookup"><span data-stu-id="59e40-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="59e40-134">为了在服务器上支持此操作, 需要进行其他配置:</span><span class="sxs-lookup"><span data-stu-id="59e40-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="59e40-135">Cookie 和持有者令牌</span><span class="sxs-lookup"><span data-stu-id="59e40-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="59e40-136">由于 cookie 是特定于浏览器的, 因此从其他类型的客户端发送这些 cookie 增加了与发送持有者令牌相比的复杂性。</span><span class="sxs-lookup"><span data-stu-id="59e40-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="59e40-137">出于此原因, 不建议使用 cookie 身份验证, 除非应用程序只需从浏览器客户端对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="59e40-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="59e40-138">当使用浏览器客户端之外的客户端时, 建议使用持有者令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="59e40-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="59e40-139">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="59e40-139">Windows authentication</span></span>

<span data-ttu-id="59e40-140">如果在你的应用中配置了[Windows 身份验证](xref:security/authentication/windowsauth), 则 SignalR 可以使用该标识来保护中心。</span><span class="sxs-lookup"><span data-stu-id="59e40-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="59e40-141">但是, 若要将消息发送给单个用户, 则需要添加自定义用户 ID 提供程序。</span><span class="sxs-lookup"><span data-stu-id="59e40-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="59e40-142">这是因为 Windows 身份验证系统不提供 SignalR 用来确定用户名的 "名称标识符" 声明。</span><span class="sxs-lookup"><span data-stu-id="59e40-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="59e40-143">添加一个新类, 该类`IUserIdProvider`实现并检索用户要用作标识符的声明之一。</span><span class="sxs-lookup"><span data-stu-id="59e40-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="59e40-144">例如, 若要使用 "名称" 声明 (这是窗体`[Domain]\[Username]`中的 Windows 用户名), 请创建以下类:</span><span class="sxs-lookup"><span data-stu-id="59e40-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="59e40-145">您可以使用 (例如 Windows SID 标识符等) `User`中的任何值,而不是。`ClaimTypes.Name`</span><span class="sxs-lookup"><span data-stu-id="59e40-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="59e40-146">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="59e40-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="59e40-147">否则, 用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="59e40-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="59e40-148">在`Startup.ConfigureServices`方法中注册此组件。</span><span class="sxs-lookup"><span data-stu-id="59e40-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="59e40-149">在 .NET 客户端中, 必须通过设置[UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)属性来启用 Windows 身份验证:</span><span class="sxs-lookup"><span data-stu-id="59e40-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="59e40-150">使用 Microsoft Internet Explorer 或 Microsoft Edge 时, 浏览器客户端仅支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="59e40-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="59e40-151">使用声明自定义标识处理</span><span class="sxs-lookup"><span data-stu-id="59e40-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="59e40-152">对用户进行身份验证的应用可以从用户声明派生 SignalR 用户 Id。</span><span class="sxs-lookup"><span data-stu-id="59e40-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="59e40-153">若要指定 SignalR 创建用户 id 的方式`IUserIdProvider` , 请实现并注册实现。</span><span class="sxs-lookup"><span data-stu-id="59e40-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="59e40-154">示例代码演示了如何使用声明选择用户的电子邮件地址作为识别属性。</span><span class="sxs-lookup"><span data-stu-id="59e40-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="59e40-155">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="59e40-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="59e40-156">否则, 用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="59e40-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="59e40-157">帐户注册会将类型`ClaimsTypes.Email`为的声明添加到 ASP.NET 标识数据库。</span><span class="sxs-lookup"><span data-stu-id="59e40-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="59e40-158">在中`Startup.ConfigureServices`注册此组件。</span><span class="sxs-lookup"><span data-stu-id="59e40-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="59e40-159">授权用户访问集线器和集线器方法</span><span class="sxs-lookup"><span data-stu-id="59e40-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="59e40-160">默认情况下, 集线器中的所有方法都可由未经身份验证的用户调用。</span><span class="sxs-lookup"><span data-stu-id="59e40-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="59e40-161">为了要求身份验证, 请将[授权](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性应用于中心:</span><span class="sxs-lookup"><span data-stu-id="59e40-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="59e40-162">您可以使用该`[Authorize]`属性的构造函数参数和属性, 将访问权限限制为仅匹配特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="59e40-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="59e40-163">例如, 如果你有一个名`MyAuthorizationPolicy`为的自定义授权策略, 则可以确保只有符合该策略的用户才能使用以下代码访问该中心:</span><span class="sxs-lookup"><span data-stu-id="59e40-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="59e40-164">单个集线器方法也可以应用`[Authorize]`该属性。</span><span class="sxs-lookup"><span data-stu-id="59e40-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="59e40-165">如果当前用户与应用于方法的策略不匹配, 则会向调用方返回错误:</span><span class="sxs-lookup"><span data-stu-id="59e40-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="59e40-166">使用授权处理程序自定义集线器方法授权</span><span class="sxs-lookup"><span data-stu-id="59e40-166">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="59e40-167">当集线器方法要求授权时, SignalR 向授权处理程序提供自定义资源。</span><span class="sxs-lookup"><span data-stu-id="59e40-167">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="59e40-168">资源是的`HubInvocationContext`实例。</span><span class="sxs-lookup"><span data-stu-id="59e40-168">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="59e40-169">`HubInvocationContext` 包括、正在调用的集线器方法的名称,以及`HubCallerContext`中心方法的参数。</span><span class="sxs-lookup"><span data-stu-id="59e40-169">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="59e40-170">请考虑允许通过 Azure Active Directory 多个组织登录的聊天室的示例。</span><span class="sxs-lookup"><span data-stu-id="59e40-170">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="59e40-171">拥有 Microsoft 帐户的任何人都可以登录到聊天, 但只有拥有组织的成员才能阻止用户或查看用户的聊天历史记录。</span><span class="sxs-lookup"><span data-stu-id="59e40-171">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="59e40-172">而且, 我们可能希望限制某些用户的某些功能。</span><span class="sxs-lookup"><span data-stu-id="59e40-172">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="59e40-173">使用 ASP.NET Core 3.0 中的更新功能, 这是完全可能的。</span><span class="sxs-lookup"><span data-stu-id="59e40-173">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="59e40-174">请注意如何`DomainRestrictedRequirement`充当自定义。 `IAuthorizationRequirement`</span><span class="sxs-lookup"><span data-stu-id="59e40-174">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="59e40-175">既然正在传入资源参数, 内部逻辑就可以检查正在调用中心的上下文, 并决定是否允许用户执行单个集线器方法。 `HubInvocationContext`</span><span class="sxs-lookup"><span data-stu-id="59e40-175">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

<span data-ttu-id="59e40-176">在`Startup.ConfigureServices`中, 添加新策略, 并将自`DomainRestrictedRequirement`定义要求用作创建`DomainRestricted`策略的参数。</span><span class="sxs-lookup"><span data-stu-id="59e40-176">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="59e40-177">在前面的示例中, `DomainRestrictedRequirement`类`IAuthorizationRequirement`既是又`AuthorizationHandler`是该要求的。</span><span class="sxs-lookup"><span data-stu-id="59e40-177">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="59e40-178">可以将这两个组件拆分为单独的类, 以分隔问题。</span><span class="sxs-lookup"><span data-stu-id="59e40-178">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="59e40-179">该示例方法的优点是, 无需在启动`AuthorizationHandler`过程中注入, 因为要求和处理程序是相同的。</span><span class="sxs-lookup"><span data-stu-id="59e40-179">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="59e40-180">其他资源</span><span class="sxs-lookup"><span data-stu-id="59e40-180">Additional resources</span></span>

* [<span data-ttu-id="59e40-181">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="59e40-181">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="59e40-182">基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="59e40-182">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
