---
title: ASP.NET Core Blazor 身份验证和授权
author: guardrex
description: 了解 Blazor 身份验证和授权方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: c07ffdbd5df58d6b3d19a5d75ce224d830101eac
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447420"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor 身份验证和授权

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

ASP.NET Core 支持 Blazor 应用程序的安全配置和管理。

Blazor 服务器和 Blazor WebAssembly 应用的安全方案存在差异。 由于 Blazor 服务器应用在服务器上运行，因此通过授权检查可以确定以下内容：

* 向用户呈现的 UI 选项（例如，用户可以使用哪些菜单条目）。
* 应用程序和组件区域的访问规则。

Blazor WebAssembly 应用在客户端上运行。 授权仅用于确定要显示的 UI 选项  。 由于用户可以修改或绕过客户端检查，因此 Blazor WebAssembly 应用无法强制执行授权访问规则。

## <a name="authentication"></a>身份验证

Blazor 使用现有的 ASP.NET Core 身份验证机制来确立用户的身份。 具体机制取决于在 Blazor 服务器或 Blazor WebAssembly 托管 Blazor 应用的方式。

### <a name="blazor-server-authentication"></a>Blazor 服务器身份验证

Blazor 服务器应用通过使用 SignalR 创建的实时连接执行操作。 建立连接后，将处理[基于 SignalR 的应用程序的身份验证](xref:signalr/authn-and-authz)。 可以基于 cookie 或一些其他持有者令牌进行身份验证。

创建项目后，Blazor 服务器项目模板可以为你设置身份验证。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

在“创建新的 ASP.NET Core Web 应用程序”  对话框中选择“Blazor 服务器应用”  模板后，在“身份验证”  下选择“更改”  。

此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：

* **无身份验证**
* **个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：
  * 使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。
  * 使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。
* **工作或学校帐户**
* **Windows 身份验证**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。

| 身份验证机制                                                                 | `{AUTHENTICATION}` 值 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 无身份验证                                                                        | `None`                   |
| 个人<br>使用 ASP.NET Core Identity 将用户存储在应用程序中。                        | `Individual`             |
| 个人<br>用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。 | `IndividualB2C`          |
| 工作或学校帐户<br>对一个租户进行组织身份验证。            | `SingleOrg`              |
| 工作或学校帐户<br>对多个租户进行组织身份验证。           | `MultiOrg`               |
| Windows 身份验证                                                                   | `Windows`                |

该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。 有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor WebAssembly 身份验证

在 Blazor WebAssembly 应用中，可绕过身份验证检查，因为用户可修改所有客户端代码。 所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。

向应用的项目文件中添加 [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 的包引用。

以下各节介绍了如何实现 Blazor WebAssembly 应用的自定义 `AuthenticationStateProvider` 服务。

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider 服务

Blazor 服务器应用包含一个内置 `AuthenticationStateProvider` 服务，可从 ASP.NET Core 的 `HttpContext.User` 获取身份验证状态数据。 这就是身份验证状态与现有 ASP.NET Core 服务器端身份验证机制的集成。

`AuthenticationStateProvider` 是 `AuthorizeView` 组件和 `CascadingAuthenticationState` 组件用于获取身份验证状态的基础服务。

通常不直接使用 `AuthenticationStateProvider`。 使用本文后面介绍的 [AuthorizeView 组件](#authorizeview-component) 或 [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 方法。 直接使用 `AuthenticationStateProvider` 的主要缺点是，如果基础身份验证状态数据发生更改，不会自动通知组件。

`AuthenticationStateProvider` 服务可以提供当前用户的 <xref:System.Security.Claims.ClaimsPrincipal> 数据，如以下示例所示：

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

由于用户是 <xref:System.Security.Claims.ClaimsPrincipal>，如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。

有关依赖关系注入 (DI) 和服务的详细信息，请参阅<xref:blazor/dependency-injection>和 <xref:fundamentals/dependency-injection>。

## <a name="implement-a-custom-authenticationstateprovider"></a>现自定义 AuthenticationStateProvider

如果你正在生成 Blazor WebAssembly 应用，或者如果你的应用规范确实需要自定义提供程序，可实现提供程序并覆盖 `GetAuthenticationStateAsync`：

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
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
}
```

在 Blazor WebAssembly 应用中，`CustomAuthStateProvider` 服务已在 Program.cs  的 `Main` 中注册：

```csharp
using Microsoft.AspNetCore.Blazor.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

使用 `CustomAuthStateProvider` 后，通过用户名 `mrfibuli` 对所有用户进行身份验证。

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>公开身份验证状态作为级联参数

如果过程逻辑需要身份验证状态数据（例如在执行用户触发的操作时），请通过定义 `Task<AuthenticationState>` 类型的级联参数来获取身份验证状态数据：

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> 在 Blazor WebAssembly 应用组件中，添加 `Microsoft.AspNetCore.Components.Authorization` 命名空间 (`@using Microsoft.AspNetCore.Components.Authorization`)。

如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。

使用 App.razor  文件中的 `AuthorizeRouteView` 和 `CascadingAuthenticationState` 组件设置 `Task<AuthenticationState>` 级联参数：

```razor
@using Microsoft.AspNetCore.Components.Authorization

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

将服务选项和授权添加到 `Program.Main`：

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

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

> [!NOTE]
> 在 Blazor WebAssembly 应用组件中，向本部分的示例中添加 `Microsoft.AspNetCore.Authorization` 命名空间 (`@using Microsoft.AspNetCore.Authorization`)。

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

在默认的 Blazor 服务器项目模板中，App.razor 文件演示了如何设置自定义内容  ：

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

如果应用程序确定基础身份验证状态数据已更改（例如，由于用户已注销或其他用户已更改其角色），则自定义 `AuthenticationStateProvider` 可以选择对 `AuthenticationStateProvider` 基类调用 `NotifyAuthenticationStateChanged` 方法。 这会通知身份验证状态数据（例如 `AuthorizeView`）使用者使用新数据重新呈现。

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

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Blazor WebAssembly 应用中的授权

在 Blazor WebAssembly 应用中，可绕过授权检查，因为用户可修改所有客户端代码。 所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。

**始终对客户端应用程序访问的任何 API 终结点内的服务器执行授权检查。**

## <a name="troubleshoot-errors"></a>排查错误

常见错误：

* **授权需要 Task\<AuthenticationState> 类型的级联参数。考虑使用 CascadingAuthenticationState 来提供此参数。**

* **对于 `authenticationStateTask`，收到了 `null` 值**

项目可能不是使用启用了身份验证的 Blazor 服务器模板创建的。 使用 `<CascadingAuthenticationState>` 将 UI 树的某些部分括起来，例如下面所示的 App.razor  ：

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
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [令人惊叹的 Blazor：身份验证](https://github.com/AdrienTorris/awesome-blazor#authentication)社区示例链接
