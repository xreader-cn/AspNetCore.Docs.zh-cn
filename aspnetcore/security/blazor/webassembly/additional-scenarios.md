---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: ccb512392341e3eea33f4ab45740b7900f7b63f9
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989460"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly 其他安全方案

作者： [Javier Calvarro 使用](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a>处理令牌请求错误

当单页面应用程序（SPA）使用 Open ID Connect （OIDC）对用户进行身份验证时，身份验证状态将以会话 cookie 的形式保留在 SPA 中的本地和标识提供程序（IP）中，这是由用户提供凭据.

通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。 否则，用户将在授予的令牌过期后注销。 在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。

在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。 如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。 此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。

这些方案不特定于基于令牌的身份验证。 它们是 Spa 的性质组成部分。 如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。

当应用执行对受保护资源的 API 调用时，必须注意以下事项：

* 若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。
* 即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。

当应用请求令牌时，有两个可能的结果：

* 请求成功，并且应用具有有效的令牌。
* 请求失败，应用必须重新对用户进行身份验证才能获取新令牌。

如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。 增加复杂程度的方法有多种：

* 将当前页面状态存储在会话存储中。 在 `OnInitializeAsync`期间，请检查状态是否可以恢复，然后再继续。
* 添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。
* 添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。

以下示例介绍如何：

* 在重定向到登录页之前保留状态。
* 使用查询字符串参数恢复先前状态身份验证。

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>在身份验证操作之前保存应用程序状态

在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。 当使用类似于状态容器的内容时，如果你想要在身份验证成功后还原状态，则可能会出现这种情况。 可以使用自定义身份验证状态对象来保持应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。

`Authentication` 组件（*Pages/Authentication*）：

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="request-additional-access-tokens"></a>请求其他访问令牌

大多数应用程序只需要一个访问令牌来与它们所使用的受保护资源交互。 在某些情况下，应用程序可能需要多个令牌才能与两个或更多个资源交互。 `IAccessTokenProvider.RequestToken` 方法提供重载，使应用能够使用一组给定的作用域预配令牌，如以下示例中所示：

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a>自定义应用路由

默认情况下，`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库使用下表中显示的路由来表示不同的身份验证状态。

| 路由                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | 触发登录操作。 |
| `authentication/login-callback`  | 处理任何登录操作的结果。 |
| `authentication/login-failed`    | 当登录操作因某种原因失败时显示错误消息。 |
| `authentication/logout`          | 触发注销操作。 |
| `authentication/logout-callback` | 处理注销操作的结果。 |
| `authentication/logout-failed`   | 出于某些原因，在注销操作失败时显示错误消息。 |
| `authentication/logged-out`      | 指示用户已成功注销。 |
| `authentication/profile`         | 触发操作以编辑用户配置文件。 |
| `authentication/register`        | 触发操作以注册新用户。 |

上表中显示的路由可通过 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`进行配置。 设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。

在下面的示例中，所有路径都带有 `/security`前缀。

`Authentication` 组件（*Pages/Authentication*）：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` （*Program.cs*）：

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

如果要求调用完全不同的路径，请按前面所述设置路由，并使用显式操作参数呈现 `RemoteAuthenticatorView`：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果你选择这样做，则允许你将 UI 分成不同的页面。

## <a name="customize-the-authentication-user-interface"></a>自定义身份验证用户界面

`RemoteAuthenticatorView` 包括每个身份验证状态的一组默认的 UI 段。 可以通过传入自定义 `RenderFragment`自定义每个状态。 若要在初始登录过程中自定义显示的文本，可以按如下所示更改 `RemoteAuthenticatorView`。

`Authentication` 组件（*Pages/Authentication*）：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`RemoteAuthenticatorView` 具有一个可用于下表中所示的每个身份验证路由的片段。

| 路由                            | Fragment                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
