---
title: ASP.NET核心BlazorWeb 组装其他安全方案
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501129"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET核心 Blazor WebAssembly 其他安全方案

哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>请求其他访问令牌

大多数应用仅需要访问令牌才能与其使用的受保护资源进行交互。 在某些情况下，应用可能需要多个令牌才能与两个或多个资源进行交互。

在下面的示例中，应用需要其他 Azure 活动目录 （AAD） Microsoft 图形 API 作用域来读取用户数据和发送邮件。 在 Azure AAD 门户中添加 Microsoft 图形 API 权限后，其他作用域在客户端应用`Program.Main`*（Program.cs）* 中配置。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

该方法`IAccessTokenProvider.RequestToken`提供重载，允许应用使用一组给定的范围预配令牌，如下例所示：

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`返回：

* `true`与`token`使用。
* `false`如果未检索令牌。

## <a name="handle-token-request-errors"></a>处理令牌请求错误

当单页应用程序 （SPA） 使用开放 ID 连接 （OIDC） 对用户进行身份验证时，身份验证状态将在 SPA 中本地和标识提供程序 （IP） 中以会话 Cookie 的形式维护，该状态是用户提供凭据而设置的。

IP 为用户发出的令牌通常在短时间内有效，通常大约一小时，因此客户端应用必须定期获取新令牌。 否则，用户将在授予的令牌过期后注销。 在大多数情况下，由于 IP 中的身份验证状态或"会话"，OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。

在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户显式注销了 IP。 如果用户访问`https://login.microsoftonline.com`并注销，将发生此情况。在这些情况下，应用不会立即知道用户已注销。客户端持有的任何令牌可能不再有效。 此外，在当前令牌过期后，客户端无法预配没有用户交互的新令牌。

这些方案不特定于基于令牌的身份验证。 它们是 SPA 性质的一部分。 如果删除身份验证 Cookie，则使用 Cookie 的 SPA 也无法调用服务器 API。

当应用对受保护的资源执行 API 调用时，您必须注意以下事项：

* 要预配新的访问令牌以调用 API，可能需要用户再次进行身份验证。
* 即使客户端具有看似有效的令牌，对服务器的调用也可能失败，因为令牌已被用户吊销。

当应用请求令牌时，有两种可能的结果：

* 请求成功，并且应用具有有效的令牌。
* 请求失败，应用必须再次对用户进行身份验证才能获得新令牌。

当令牌请求失败时，您需要在执行重定向之前决定是否要保存任何当前状态。 存在几种方法，复杂性越来越高：

* 将当前页面状态存储在会话存储中。 在`OnInitializeAsync`期间，检查是否可以在继续之前恢复状态。
* 添加查询字符串参数，并将其用作向应用发出信号，使其需要重新补充以前保存的状态的一种方式。
* 添加具有唯一标识符的查询字符串参数，将数据存储在会话存储中，而不会冒与其他项发生冲突的风险。

以下示例介绍如何：

* 在重定向到登录页之前保留状态。
* 使用查询字符串参数恢复以前的状态后身份验证。

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

## <a name="save-app-state-before-an-authentication-operation"></a>在身份验证操作之前保存应用状态

在身份验证操作期间，在某些情况下，您希望在浏览器重定向到 IP 之前保存应用状态。 当您使用状态容器之类的内容，并且希望在身份验证成功后还原状态时，情况可能如此。 可以使用自定义身份验证状态对象保留特定于应用的状态或对它的引用，并在身份验证操作成功完成后还原该状态。

`Authentication`组件 （*页/身份验证.razor）：*

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

## <a name="customize-app-routes"></a>自定义应用路由

默认情况下，`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库使用下表中显示的路由来表示不同的身份验证状态。

| 路由                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | 触发登录操作。 |
| `authentication/login-callback`  | 处理任何登录操作的结果。 |
| `authentication/login-failed`    | 当登录操作由于某种原因失败时显示错误消息。 |
| `authentication/logout`          | 触发注销操作。 |
| `authentication/logout-callback` | 处理注销操作的结果。 |
| `authentication/logout-failed`   | 当注销操作由于某种原因失败时显示错误消息。 |
| `authentication/logged-out`      | 指示用户已成功注销。 |
| `authentication/profile`         | 触发操作以编辑用户配置文件。 |
| `authentication/register`        | 触发操作以注册新用户。 |

上表中显示的路由可通过`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`进行配置。 设置选项以提供自定义路由时，请确认应用具有处理每个路径的路由。

在下面的示例中，所有路径都预定了`/security`。

`Authentication`组件 （*页/身份验证.razor）：*

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`*（Program.cs*）：

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

如果要求对完全不同的路径进行调用，请按照前面所述设置路由，并`RemoteAuthenticatorView`呈现显式操作参数：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果选择这样做，则可以将 UI 分解为不同的页面。

## <a name="customize-the-authentication-user-interface"></a>自定义身份验证用户界面

`RemoteAuthenticatorView`包括每个身份验证状态的默认 UI 部分集。 每个状态都可以通过传入自定义`RenderFragment`来自定义。 要在初始登录过程中自定义显示的文本，可以按如下方式更改`RemoteAuthenticatorView`。

`Authentication`组件 （*页/身份验证.razor）：*

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

有`RemoteAuthenticatorView`一个片段，可以按下表中显示的每个身份验证路由使用。

| 路由                            | 片段                |
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
