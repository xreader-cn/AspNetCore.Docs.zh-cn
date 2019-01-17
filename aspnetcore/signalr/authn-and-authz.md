---
title: 身份验证和授权在 ASP.NET Core SignalR
author: tdykstra
description: 了解如何在 ASP.NET Core SignalR 中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 06/29/2018
uid: signalr/authn-and-authz
ms.openlocfilehash: 6624b2b4361eb0a5bb6e45108878ee6fe5ecb443
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284406"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="fd35d-103">身份验证和授权在 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="fd35d-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="fd35d-104">通过[Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="fd35d-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="fd35d-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="fd35d-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="fd35d-106">连接到的 SignalR hub 的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="fd35d-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="fd35d-107">可将 SignalR 与 [ASP.NET Core 身份验证](xref:security/authentication/identity) 结合使用，将用户与每个连接相关联。</span><span class="sxs-lookup"><span data-stu-id="fd35d-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="fd35d-108">在中心，可以从 [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)属性访问身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="fd35d-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="fd35d-109">中心可借助身份验证在所有与用户关联的连接上调用方法（请参阅[在 SignalR 中管理用户和组](xref:signalr/groups)，了解相关详细信息）。</span><span class="sxs-lookup"><span data-stu-id="fd35d-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="fd35d-110">单个用户可能与多个链接相关联。</span><span class="sxs-lookup"><span data-stu-id="fd35d-110">Multiple connections may be associated with a single user.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="fd35d-111">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="fd35d-111">Cookie authentication</span></span>

<span data-ttu-id="fd35d-112">在基于浏览器的应用中，cookie 身份验证允许你现有的用户凭据以自动传递到 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="fd35d-112">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="fd35d-113">如果使用浏览器客户端，则需要无额外配置。</span><span class="sxs-lookup"><span data-stu-id="fd35d-113">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="fd35d-114">如果用户登录到你的应用，SignalR 连接会自动继承此身份验证。</span><span class="sxs-lookup"><span data-stu-id="fd35d-114">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="fd35d-115">Cookie 是特定于浏览器的方法，将发送访问令牌，但非浏览器客户端可以向他们发送。</span><span class="sxs-lookup"><span data-stu-id="fd35d-115">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="fd35d-116">使用时[.NET 客户端](xref:signalr/dotnet-client)，则`Cookies`属性可以配置在`.WithUrl`调用，以提供一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="fd35d-116">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="fd35d-117">但是，使用 cookie 身份验证从.NET 客户端要求提供要交换的 cookie 身份验证数据的 API 应用。</span><span class="sxs-lookup"><span data-stu-id="fd35d-117">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="fd35d-118">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="fd35d-118">Bearer token authentication</span></span>

<span data-ttu-id="fd35d-119">客户端可以提供访问令牌而不是使用 cookie。</span><span class="sxs-lookup"><span data-stu-id="fd35d-119">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="fd35d-120">服务器验证该令牌，并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="fd35d-120">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="fd35d-121">仅在建立连接时，才执行此验证。</span><span class="sxs-lookup"><span data-stu-id="fd35d-121">This validation is done only when the connection is established.</span></span> <span data-ttu-id="fd35d-122">连接的生命周期，服务器不会自动重新验证令牌吊销检查。</span><span class="sxs-lookup"><span data-stu-id="fd35d-122">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="fd35d-123">在服务器上，使用配置持有者令牌身份验证[JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)。</span><span class="sxs-lookup"><span data-stu-id="fd35d-123">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="fd35d-124">在 JavaScript 客户端，该令牌可以使用提供[accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)选项。</span><span class="sxs-lookup"><span data-stu-id="fd35d-124">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="fd35d-125">在.NET 客户端，没有类似[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)属性，可用于配置的令牌：</span><span class="sxs-lookup"><span data-stu-id="fd35d-125">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fd35d-126">你提供的访问令牌函数之前调用**每个**所做的 SignalR 的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="fd35d-126">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="fd35d-127">如果需要续订令牌才能保持连接处于活动状态 （因为它在连接期间可能会过期），此函数中执行此操作从并返回更新的令牌。</span><span class="sxs-lookup"><span data-stu-id="fd35d-127">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="fd35d-128">在标准 web Api，持有者令牌将发送 HTTP 标头中。</span><span class="sxs-lookup"><span data-stu-id="fd35d-128">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="fd35d-129">但是，SignalR 是无法使用某些传输通道时在浏览器中设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="fd35d-129">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="fd35d-130">使用 Websocket 和服务器发送事件时，会将令牌传输作为查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="fd35d-130">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="fd35d-131">若要在服务器上支持此功能，则需要其他配置：</span><span class="sxs-lookup"><span data-stu-id="fd35d-131">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="fd35d-132">与持有者令牌的 cookie</span><span class="sxs-lookup"><span data-stu-id="fd35d-132">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="fd35d-133">因为 cookie 是特定于浏览器，将它们发送从其他类型的客户端会增加复杂性相比发送持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="fd35d-133">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="fd35d-134">出于此原因，不被建议的 cookie 身份验证，除非应用只需要从浏览器客户端的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="fd35d-134">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="fd35d-135">使用非浏览器客户端的客户端时，持有者令牌身份验证是建议的方法。</span><span class="sxs-lookup"><span data-stu-id="fd35d-135">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="fd35d-136">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="fd35d-136">Windows authentication</span></span>

<span data-ttu-id="fd35d-137">如果[Windows 身份验证](xref:security/authentication/windowsauth)配置为在应用中，SignalR 可以使用该标识安全中心。</span><span class="sxs-lookup"><span data-stu-id="fd35d-137">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="fd35d-138">但是，若要将消息发送给单个用户，您需要添加自定义的用户 ID 提供程序。</span><span class="sxs-lookup"><span data-stu-id="fd35d-138">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="fd35d-139">这是因为 Windows 身份验证系统不提供 SignalR 使用来确定用户名称的"名称标识符"声明。</span><span class="sxs-lookup"><span data-stu-id="fd35d-139">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="fd35d-140">添加新的类实现`IUserIdProvider`和检索的声明之一中要用作标识符的用户。</span><span class="sxs-lookup"><span data-stu-id="fd35d-140">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="fd35d-141">例如，若要使用"Name"声明 (即窗体中的 Windows 用户名`[Domain]\[Username]`)，创建以下类：</span><span class="sxs-lookup"><span data-stu-id="fd35d-141">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

```csharp
public class NameUserIdProvider : IUserIdProvider
{
    public string GetUserId(HubConnectionContext connection)
    {
        return connection.User?.FindFirst(ClaimTypes.Name)?.Value;
    }
}
```

<span data-ttu-id="fd35d-142">而非`ClaimTypes.Name`，可以使用的任何值`User`（例如 Windows SID 标识符等）。</span><span class="sxs-lookup"><span data-stu-id="fd35d-142">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="fd35d-143">您选择的值必须是在系统中所有用户之间唯一的。</span><span class="sxs-lookup"><span data-stu-id="fd35d-143">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="fd35d-144">否则，适用于一个用户的消息可能最终转到不同的用户。</span><span class="sxs-lookup"><span data-stu-id="fd35d-144">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="fd35d-145">注册此组件中的您`Startup.ConfigureServices`方法**后**对的调用 `.AddSignalR`</span><span class="sxs-lookup"><span data-stu-id="fd35d-145">Register this component in your `Startup.ConfigureServices` method **after** the call to `.AddSignalR`</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="fd35d-146">在.NET 客户端，必须通过设置启用 Windows 身份验证[UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)属性：</span><span class="sxs-lookup"><span data-stu-id="fd35d-146">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="fd35d-147">使用 Microsoft Internet Explorer 或 Microsoft Edge 时，浏览器客户端仅支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="fd35d-147">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="fd35d-148">授权用户访问集线器和集线器方法</span><span class="sxs-lookup"><span data-stu-id="fd35d-148">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="fd35d-149">默认情况下，可以通过未经身份验证的用户调用一个中心中的所有方法。</span><span class="sxs-lookup"><span data-stu-id="fd35d-149">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="fd35d-150">为了要求身份验证，应用[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性为中心：</span><span class="sxs-lookup"><span data-stu-id="fd35d-150">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="fd35d-151">可以使用的构造函数参数和属性`[Authorize]`属性以限制只有匹配特定的用户访问[授权策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="fd35d-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="fd35d-152">例如，如果具有名为的自定义授权策略`MyAuthorizationPolicy`可以确保只有匹配该策略的用户可以访问的中心使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="fd35d-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="fd35d-153">单个集线器方法可以具有`[Authorize]`也应用属性。</span><span class="sxs-lookup"><span data-stu-id="fd35d-153">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="fd35d-154">如果当前用户不匹配的策略应用于方法，是向调用方返回错误：</span><span class="sxs-lookup"><span data-stu-id="fd35d-154">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
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

## <a name="additional-resources"></a><span data-ttu-id="fd35d-155">其他资源</span><span class="sxs-lookup"><span data-stu-id="fd35d-155">Additional resources</span></span>

* [<span data-ttu-id="fd35d-156">在 ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="fd35d-156">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
