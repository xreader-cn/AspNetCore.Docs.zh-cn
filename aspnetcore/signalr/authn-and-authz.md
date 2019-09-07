---
title: ASP.NET Core SignalR 中的身份验证和授权
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR 中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 07/15/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: da226f4e192be8e34a0b2cec1493a1353c995279
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746532"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core SignalR 中的身份验证和授权

作者： [Andrew Stanton](https://twitter.com/anurse)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>对连接到 SignalR 中心的用户进行身份验证

可将 SignalR 与 [ASP.NET Core 身份验证](xref:security/authentication/identity) 结合使用，将用户与每个连接相关联。 在中心，可以从 [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)属性访问身份验证数据。 中心可借助身份验证在所有与用户关联的连接上调用方法（请参阅[在 SignalR 中管理用户和组](xref:signalr/groups)，了解相关详细信息）。 单个用户可能与多个链接相关联。

下面是一个示例`Startup.Configure` ，它使用 SignalR 和 ASP.NET Core authentication：

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
> 注册 SignalR 和 ASP.NET Core 身份验证中间件的顺序。 始终调用`UseAuthentication` `HttpContext`， `UseSignalR`以便 SignalR 在上有用户。

::: moniker-end

### <a name="cookie-authentication"></a>Cookie 身份验证

在基于浏览器的应用中，凭借 cookie 身份验证，现有用户凭据可以自动传递到 SignalR 连接 。 使用浏览器客户端时，无需额外配置。 如果用户登录到你的应用，SignalR 连接便会自动继承此身份验证。

Cookie 是一种特定于浏览器的发送访问令牌的方式，但非浏览器客户端也可以发送这些令牌。 使用 [.NET 客户端](xref:signalr/dotnet-client) 时，可以在 `.WithUrl` 调用中配置 `Cookies` 属性来提供 cookie。 但是，使用 .NET 客户端的 cookie 身份验证时，应用需要提供 API 来交换 cookie 的身份验证数据。

### <a name="bearer-token-authentication"></a>持有者令牌身份验证

客户端可以提供访问令牌，而不是使用 cookie。 服务器验证令牌并使用它来标识用户。 仅在建立连接时才执行此验证。 连接开启后，服务器不会通过自动重新验证来检查令牌是否撤销。

在服务器上，持有者令牌身份验证使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)进行配置。

在 JavaScript 客户端中，可使用[accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)选项提供令牌。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

在 .NET 客户端中，有一个类似的[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)属性可用于配置令牌：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 提供的访问令牌函数在 SignalR 发出的**每个**HTTP 请求之前调用。 如果你需要续订标记以便使连接保持活动状态（因为它可能会在连接期间过期），请在此函数中执行此操作，并返回已更新的令牌。

在标准 web Api 中，持有者令牌是在 HTTP 标头中发送的。 但是，在使用某些传输时，SignalR 无法在浏览器中设置这些标头。 使用 Websocket 和服务器发送事件时，会将令牌作为查询字符串参数进行传输。 为了在服务器上支持此操作，需要进行其他配置：

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Cookie 和持有者令牌 

由于 cookie 是特定于浏览器的，因此从其他类型的客户端发送这些 cookie 增加了与发送持有者令牌相比的复杂性。 出于此原因，不建议使用 cookie 身份验证，除非应用程序只需从浏览器客户端对用户进行身份验证。 当使用浏览器客户端之外的客户端时，建议使用持有者令牌身份验证。

### <a name="windows-authentication"></a>Windows 身份验证

如果在你的应用中配置了[Windows 身份验证](xref:security/authentication/windowsauth)，则 SignalR 可以使用该标识来保护中心。 但是，若要将消息发送给单个用户，则需要添加自定义用户 ID 提供程序。 这是因为 Windows 身份验证系统不提供 SignalR 用来确定用户名的 "名称标识符" 声明。

添加一个新类，该类`IUserIdProvider`实现并检索用户要用作标识符的声明之一。 例如，若要使用 "名称" 声明（这是窗体`[Domain]\[Username]`中的 Windows 用户名），请创建以下类：

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

您可以使用（例如 Windows SID 标识符等） `User`中的任何值，而不是。`ClaimTypes.Name`

> [!NOTE]
> 您选择的值在系统中的所有用户中必须是唯一的。 否则，用于一个用户的消息可能最终会转到其他用户。

在`Startup.ConfigureServices`方法中注册此组件。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

在 .NET 客户端中，必须通过设置[UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)属性来启用 Windows 身份验证：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

使用 Microsoft Internet Explorer 或 Microsoft Edge 时，浏览器客户端仅支持 Windows 身份验证。

### <a name="use-claims-to-customize-identity-handling"></a>使用声明自定义标识处理

对用户进行身份验证的应用可以从用户声明派生 SignalR 用户 Id。 若要指定 SignalR 创建用户 id 的方式`IUserIdProvider` ，请实现并注册实现。

示例代码演示了如何使用声明选择用户的电子邮件地址作为识别属性。 

> [!NOTE]
> 您选择的值在系统中的所有用户中必须是唯一的。 否则，用于一个用户的消息可能最终会转到其他用户。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

帐户注册会将类型`ClaimsTypes.Email`为的声明添加到 ASP.NET 标识数据库。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

在中`Startup.ConfigureServices`注册此组件。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>授权用户访问集线器和集线器方法

默认情况下，集线器中的所有方法都可由未经身份验证的用户调用。 为了要求身份验证，请将[授权](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性应用于中心：

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

您可以使用该`[Authorize]`属性的构造函数参数和属性，将访问权限限制为仅匹配特定[授权策略](xref:security/authorization/policies)的用户。 例如，如果你有一个名`MyAuthorizationPolicy`为的自定义授权策略，则可以确保只有符合该策略的用户才能使用以下代码访问该中心：

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

单个集线器方法也可以应用`[Authorize]`该属性。 如果当前用户与应用于方法的策略不匹配，则会向调用方返回错误：

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>使用授权处理程序自定义集线器方法授权

当集线器方法要求授权时，SignalR 向授权处理程序提供自定义资源。 资源是的`HubInvocationContext`实例。 `HubInvocationContext` 包括、正在调用的集线器方法的名称，以及`HubCallerContext`中心方法的参数。

请考虑允许通过 Azure Active Directory 多个组织登录的聊天室的示例。 拥有 Microsoft 帐户的任何人都可以登录到聊天，但只有拥有组织的成员才能阻止用户或查看用户的聊天历史记录。 而且，我们可能希望限制某些用户的某些功能。 使用 ASP.NET Core 3.0 中的更新功能，这是完全可能的。 请注意如何`DomainRestrictedRequirement`充当自定义。 `IAuthorizationRequirement` 既然正在传入资源参数，内部逻辑就可以检查正在调用中心的上下文，并决定是否允许用户执行单个集线器方法。 `HubInvocationContext`

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

在`Startup.ConfigureServices`中，添加新策略，并将自`DomainRestrictedRequirement`定义要求用作创建`DomainRestricted`策略的参数。

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

在前面的示例中， `DomainRestrictedRequirement`类`IAuthorizationRequirement`既是又`AuthorizationHandler`是该要求的。 可以将这两个组件拆分为单独的类，以分隔问题。 该示例方法的优点是，无需在启动`AuthorizationHandler`过程中注入，因为要求和处理程序是相同的。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core 中的持有者令牌身份验证](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [基于资源的授权](xref:security/authorization/resourcebased)
