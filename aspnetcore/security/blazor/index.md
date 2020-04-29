---
title: ASP.NET Core Blazor 身份验证和授权
author: guardrex
description: 了解 Blazor 身份验证和授权方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: ced8e90147b08bc75aec4534fdd8d8552506f88c
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206094"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor 身份验证和授权

作者：[Steve Sanderson](https://github.com/SteveSandersonMS) 及 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> 对于本文中适用于 Blazor WebAssembly 的指导，ASP.NET Core Blazor WebAssembly 模板必须是 3.2 或更高版本。 如果不使用 Visual Studio 版本 16.6 预览版 2 或更高版本，可按照以下 <xref:blazor/get-started> 中的指导获取最新的 Blazor WebAssembly 模板。

ASP.NET Core 支持 Blazor 应用中的安全配置和管理。

Blazor 服务器和 Blazor WebAssembly 应用的安全方案存在差异。 由于 Blazor 服务器应用在服务器上运行，因此通过授权检查可确定以下内容：

* 向用户呈现的 UI 选项（例如，用户可以使用哪些菜单条目）。
* 应用程序和组件区域的访问规则。

Blazor WebAssembly 应用在客户端上运行。 授权仅用于确定要显示的 UI 选项  。 由于用户可修改或绕过客户端检查，因此 Blazor WebAssembly 应用无法强制执行授权访问规则。

[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization)不适用于可路由的 Razor 组件。 如果非可路由的 Razor 组件[嵌入在页面中](xref:blazor/integrate-components#render-components-from-a-page-or-view)，则页面的授权约定会间接影响 Razor 组件以及其余页面内容。

> [!NOTE]
> Razor 组件中不支持 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 和 <xref:Microsoft.AspNetCore.Identity.UserManager%601>。

## <a name="authentication"></a>身份验证

Blazor 使用现有的 ASP.NET Core 身份验证机制来确立用户的身份。 具体机制取决于是用 Blazor WebAssembly 还是 Blazor 服务器托管 Blazor 应用。

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly 身份验证

在 Blazor WebAssembly 应用中，可绕过身份验证检查，因为用户可修改所有客户端代码。 所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。

添加以下内容：

* 在应用的项目文件中添加 [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 的包引用。
* 在应用的 *_Imports.razor* 文件中添加 `Microsoft.AspNetCore.Components.Authorization` 命名空间。

为处理身份验证，需实现内置或自定义 `AuthenticationStateProvider` 服务，以下几节对此进行了介绍。

有关创建应用和配置的详细信息，请参阅 <xref:security/blazor/webassembly/index>。

### <a name="blazor-server-authentication"></a>Blazor 服务器身份验证

Blazor 服务器应用通过使用 SignalR 创建的实时连接执行操作。 建立连接后，将处理[基于 SignalR 的应用的身份验证](xref:signalr/authn-and-authz)。 可以基于 cookie 或一些其他持有者令牌进行身份验证。

有关创建应用和配置的详细信息，请参阅 <xref:security/blazor/server/index>。

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider 服务

内置的 `AuthenticationStateProvider` 服务可从 ASP.NET Core 的 `HttpContext.User` 获取身份验证状态数据。 身份验证状态就是这样与现有 ASP.NET Core 身份验证机制集成。

`AuthenticationStateProvider` 是 `AuthorizeView` 组件和 `CascadingAuthenticationState` 组件用于获取身份验证状态的基础服务。

通常不直接使用 `AuthenticationStateProvider`。 使用本文后面介绍的 [AuthorizeView 组件](#authorizeview-component) 或 [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 方法。 直接使用 `AuthenticationStateProvider` 的主要缺点是，如果基础身份验证状态数据发生更改，不会自动通知组件。

`AuthenticationStateProvider` 服务可以提供当前用户的 <xref:System.Security.Claims.ClaimsPrincipal> 数据，如以下示例所示：

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

由于用户是 <xref:System.Security.Claims.ClaimsPrincipal>，如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。

有关依赖关系注入 (DI) 和服务的详细信息，请参阅<xref:blazor/dependency-injection>和 <xref:fundamentals/dependency-injection>。

## <a name="implement-a-custom-authenticationstateprovider"></a>现自定义 AuthenticationStateProvider

如果应用需要自定义提供程序，请实现 `AuthenticationStateProvider` 并替代 `GetAuthenticationStateAsync`：

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

在 Blazor WebAssembly 应用中，`CustomAuthStateProvider` 服务已在 Program.cs  的 `Main` 中注册：

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

在 Blazor 服务器应用中，在 `Startup.ConfigureServices`中注册 `CustomAuthStateProvider` 服务：

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

使用以上示例中的 `CustomAuthStateProvider`，通过用户名 `mrfibuli` 对所有用户进行身份验证。

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>公开身份验证状态作为级联参数

如果过程逻辑需要身份验证状态数据（例如在执行用户触发的操作时），请通过定义 `Task<AuthenticationState>` 类型的级联参数来获取身份验证状态数据：

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。

使用 `App` 组件 (App.razor )  中的 `AuthorizeRouteView` 和 `CascadingAuthenticationState` 组件设置 `Task<AuthenticationState>` 级联参数：

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

在 Blazor WebAssembly 应用中，将服务选项和授权添加到 `Program.Main`：

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

在 Blazor 服务器应用中，已存在选项和授权服务，因此无需进一步操作。

## <a name="authorization"></a>授权

对用户进行身份验证后，应用授权规则来控制用户可以执行的操作  。

通常根据以下几点确定是授权访问还是拒绝访问：

* 已对用户进行身份验证（已登录）。
* 用户属于某个角色  。
* 用户具有声明  。
* 满足策略要求  。

上述所有概念都与 ASP.NET Core MVC 或 Razor Pages 应用程序中的概念相同。 有关 ASP.NET Core 安全性的详细信息，请参阅 [ASP.NET Core 安全性和标识](xref:security/index)下的文章。

## <a name="authorizeview-component"></a>AuthorizeView 组件

`AuthorizeView` 组件根据用户是否有权查看来选择性地显示 UI。 如果只需要为用户显示数据，而不需要在过程逻辑中使用用户的标识，那么此方法很有用  。

该组件公开了一个 `AuthenticationState` 类型的 `context` 变量，可以使用该变量来访问有关已登录用户的信息：

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

另外，如果用户未经过身份验证，你还可以提供不同的内容以供显示：

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

可以在 `NavMenu` 组件 (Shared/NavMenu.razor)  中使用 `AuthorizeView` 组件来显示 `NavLink` 的列表项 (`<li>...</li>`)，但请注意，此方法仅从呈现的输出中删除列表项。 它不会阻止用户导航到该组件。

`<Authorized>` 和 `<NotAuthorized>` 标记的内容可以包括任意项，如其他交互式组件。

[授权](#authorization)一节中介绍了授权条件，如用于控制 UI 选项或访问权限的角色或策略。

如果未指定授权条件，则 `AuthorizeView` 使用默认策略，并且：

* 将经过身份验证（已登录）的用户视为已授权。
* 将未经过身份验证（已注销）的用户视为未授权。

### <a name="role-based-and-policy-based-authorization"></a>基于角色和基于策略的授权

`AuthorizeView` 组件支持基于角色或基于策略的授权   。

对于基于角色的授权，请使用 `Roles` 参数：

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

有关详细信息，请参阅 <xref:security/authorization/roles>。

对于基于策略的授权，请使用 `Policy` 参数：

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

基于策略的授权包含一个特例，即基于声明的授权。 例如，可以定义一个要求用户具有特定声明的策略。 有关详细信息，请参阅 <xref:security/authorization/policies>。

这些 API 可用于 Blazor 服务器或 Blazor WebAssembly 应用。

如果 `Roles` 或 `Policy` 均未指定，则 `AuthorizeView` 使用默认策略。

### <a name="content-displayed-during-asynchronous-authentication"></a>异步身份验证期间显示的内容

通过 Blazor，可通过异步方式确定身份验证状态  。 此方法的主要应用场景是 Blazor WebAssembly 应用向外部终结点发出请求来进行身份验证。

正在进行身份验证时，`AuthorizeView` 默认情况下不显示任何内容。 若要在进行身份验证期间显示内容，请使用 `<Authorizing>` 元素：

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

此方法通常不适用于 Blazor 服务器应用。 身份验证状态一经确立，Blazor 服务器应用便会立即获知身份验证状态。 `Authorizing` 内容可在 Blazor 服务器应用的 `AuthorizeView` 组件中提供，但该内容永远不会显示。

## <a name="authorize-attribute"></a>[Authorize] 属性

`[Authorize]` 属性可在 Razor 组件中使用：

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> 仅对通过 Blazor 路由器到达的 `@page` 组件使用 `[Authorize]`。 授权仅作为路由的一个方面执行，而不是作为页面中呈现的子组件来执行  。 若要授权在页面中显示特定部分，请改用 `AuthorizeView`。

`[Authorize]` 属性还支持基于角色或基于策略的授权。 对于基于角色的授权，请使用 `Roles` 参数：

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

对于基于策略的授权，请使用 `Policy` 参数：

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

如果 `Roles` 或 `Policy` 均未指定，则 `[Authorize]` 使用默认策略，默认情况下，它会：

* 将经过身份验证（已登录）的用户视为已授权。
* 将未经过身份验证（已注销）的用户视为未授权。

## <a name="customize-unauthorized-content-with-the-router-component"></a>使用路由器组件自定义未授权的内容

`Router` 组件与 `AuthorizeRouteView` 组件搭配使用时，可允许应用程序在以下情况下指定自定义内容：

* 找不到内容。
* 用户不符合应用于组件的 `[Authorize]` 条件。 [`[Authorize]` 属性](#authorize-attribute)一节中介绍了 `[Authorize]` 属性。
* 正在进行异步身份验证。

在默认的 Blazor 服务器项目模板中，`App` 组件 (App.razor) 演示了如何设置自定义内容：

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

`<NotFound>`、`<NotAuthorized>` 和 `<Authorizing>` 标记的内容可以包括任意项，如其他交互式组件。

如果未指定 `<NotAuthorized>` 元素，`AuthorizeRouteView` 就会使用以下回退消息：

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>有关身份验证状态更改的通知

如果应用确定基础身份验证状态数据已更改（例如由于用户注销或其他用户更改了其角色），自定义 [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) 可以选择对 `AuthenticationStateProvider` 基类调用 `NotifyAuthenticationStateChanged` 方法。 这会通知身份验证状态数据（例如 `AuthorizeView`）使用者使用新数据重新呈现。

## <a name="procedural-logic"></a>过程逻辑

如果作为过程逻辑的一部分应用程序需要检查授权规则，请使用 `Task<AuthenticationState>` 类型的级联参数获取用户的 <xref:System.Security.Claims.ClaimsPrincipal>。 `Task<AuthenticationState>` 可以与其他服务（例如 `IAuthorizationService`）结合使用以评估策略。

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> 在 Blazor WebAssembly 应用组件中，添加 `Microsoft.AspNetCore.Authorization` 和 `Microsoft.AspNetCore.Components.Authorization` 命名空间：
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> 可以通过将这些命名空间添加到应用的 _Imports razor 文件来全局提供它们。 

## <a name="authorization-in-blazor-webassembly-apps"></a>Blazor WebAssembly 应用中的授权

在 Blazor WebAssembly 应用中，可绕过授权检查，因为用户可修改所有客户端代码。 所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。

**始终对客户端应用程序访问的任何 API 终结点内的服务器执行授权检查。**

有关详细信息，请参阅 <xref:security/blazor/webassembly/index> 下的文章。

## <a name="troubleshoot-errors"></a>排查错误

常见错误：

* **授权需要 Task\<AuthenticationState> 类型的级联参数。考虑使用 CascadingAuthenticationState 来提供此参数。**

* **对于 `authenticationStateTask`，收到了 `null` 值**

项目可能不是使用启用了身份验证的 Blazor 服务器模板创建的。 使用 `<CascadingAuthenticationState>` 将 UI 树的某些部分括起来，例如下面 `App` 组件 (App.razor) 中所示  ：

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` 提供 `Task<AuthenticationState>` 级联参数，这是它从基础 `AuthenticationStateProvider` DI 服务那里接收的参数。

## <a name="additional-resources"></a>其他资源

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [令人惊叹的 Blazor：身份验证](https://github.com/AdrienTorris/awesome-blazor#authentication)社区示例链接
