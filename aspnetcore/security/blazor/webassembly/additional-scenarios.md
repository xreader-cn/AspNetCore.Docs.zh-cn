---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: fe87ce76d8e181de788188b021616f2a09833585
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083691"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="553f5-102">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="553f5-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="553f5-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="553f5-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a><span data-ttu-id="553f5-104">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="553f5-104">Handle token request errors</span></span>

<span data-ttu-id="553f5-105">当单个页面应用（SPA）使用开放 ID Connect （OIDC）对用户进行身份验证时，身份验证状态将以本地用户提供其凭据的用户的会话 cookie 的形式保留在 SPA 内的 SPA 和标识提供程序（IP）中。</span><span class="sxs-lookup"><span data-stu-id="553f5-105">When a single page app (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="553f5-106">通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="553f5-106">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="553f5-107">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="553f5-107">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="553f5-108">在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。</span><span class="sxs-lookup"><span data-stu-id="553f5-108">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="553f5-109">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。</span><span class="sxs-lookup"><span data-stu-id="553f5-109">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="553f5-110">如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="553f5-110">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="553f5-111">此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="553f5-111">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="553f5-112">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="553f5-112">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="553f5-113">它们是 Spa 的性质组成部分。</span><span class="sxs-lookup"><span data-stu-id="553f5-113">They are part of the nature of SPAs.</span></span> <span data-ttu-id="553f5-114">如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="553f5-114">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="553f5-115">当应用执行对受保护资源的 API 调用时，必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="553f5-115">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="553f5-116">若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="553f5-116">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="553f5-117">即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。</span><span class="sxs-lookup"><span data-stu-id="553f5-117">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="553f5-118">当应用请求令牌时，有两个可能的结果：</span><span class="sxs-lookup"><span data-stu-id="553f5-118">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="553f5-119">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="553f5-119">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="553f5-120">请求失败，应用必须重新对用户进行身份验证才能获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="553f5-120">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="553f5-121">如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-121">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="553f5-122">增加复杂程度的方法有多种：</span><span class="sxs-lookup"><span data-stu-id="553f5-122">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="553f5-123">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="553f5-123">Store the current page state in session storage.</span></span> <span data-ttu-id="553f5-124">在 `OnInitializeAsync`期间，请检查状态是否可以恢复，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="553f5-124">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="553f5-125">添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-125">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="553f5-126">添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="553f5-126">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="553f5-127">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="553f5-127">The following example shows how to:</span></span>

* <span data-ttu-id="553f5-128">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-128">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="553f5-129">使用查询字符串参数恢复先前状态身份验证。</span><span class="sxs-lookup"><span data-stu-id="553f5-129">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="553f5-130">在身份验证操作之前保存应用程序状态</span><span class="sxs-lookup"><span data-stu-id="553f5-130">Save app state before an authentication operation</span></span>

<span data-ttu-id="553f5-131">在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-131">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="553f5-132">当使用类似于状态容器的内容时，如果你想要在身份验证成功后还原状态，则可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="553f5-132">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="553f5-133">可以使用自定义身份验证状态对象来保持应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-133">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="553f5-134">`Authentication` 组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="553f5-134">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="request-additional-access-tokens"></a><span data-ttu-id="553f5-135">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="553f5-135">Request additional access tokens</span></span>

<span data-ttu-id="553f5-136">大多数应用程序只需要一个访问令牌来与它们所使用的受保护资源交互。</span><span class="sxs-lookup"><span data-stu-id="553f5-136">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="553f5-137">在某些情况下，应用程序可能需要多个令牌才能与两个或更多个资源交互。</span><span class="sxs-lookup"><span data-stu-id="553f5-137">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span> <span data-ttu-id="553f5-138">`IAccessTokenProvider.RequestToken` 方法提供重载，使应用能够使用一组给定的作用域预配令牌，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="553f5-138">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a><span data-ttu-id="553f5-139">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="553f5-139">Customize app routes</span></span>

<span data-ttu-id="553f5-140">默认情况下，`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-140">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="553f5-141">路由</span><span class="sxs-lookup"><span data-stu-id="553f5-141">Route</span></span>                            | <span data-ttu-id="553f5-142">目的</span><span class="sxs-lookup"><span data-stu-id="553f5-142">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="553f5-143">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="553f5-143">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="553f5-144">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="553f5-144">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="553f5-145">当登录操作因某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="553f5-145">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="553f5-146">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="553f5-146">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="553f5-147">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="553f5-147">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="553f5-148">出于某些原因，在注销操作失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="553f5-148">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="553f5-149">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="553f5-149">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="553f5-150">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="553f5-150">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="553f5-151">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="553f5-151">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="553f5-152">上表中显示的路由可通过 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`进行配置。</span><span class="sxs-lookup"><span data-stu-id="553f5-152">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="553f5-153">设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="553f5-153">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="553f5-154">在下面的示例中，所有路径都带有 `/security`前缀。</span><span class="sxs-lookup"><span data-stu-id="553f5-154">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="553f5-155">`Authentication` 组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="553f5-155">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="553f5-156">`Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="553f5-156">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="553f5-157">如果要求调用完全不同的路径，请按前面所述设置路由，并使用显式操作参数呈现 `RemoteAuthenticatorView`：</span><span class="sxs-lookup"><span data-stu-id="553f5-157">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="553f5-158">如果你选择这样做，则允许你将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="553f5-158">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="553f5-159">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="553f5-159">Customize the authentication user interface</span></span>

<span data-ttu-id="553f5-160">`RemoteAuthenticatorView` 包括每个身份验证状态的一组默认的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="553f5-160">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="553f5-161">可以通过传入自定义 `RenderFragment`自定义每个状态。</span><span class="sxs-lookup"><span data-stu-id="553f5-161">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="553f5-162">若要在初始登录过程中自定义显示的文本，可以按如下所示更改 `RemoteAuthenticatorView`。</span><span class="sxs-lookup"><span data-stu-id="553f5-162">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="553f5-163">`Authentication` 组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="553f5-163">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="553f5-164">`RemoteAuthenticatorView` 具有一个可用于下表中所示的每个身份验证路由的片段。</span><span class="sxs-lookup"><span data-stu-id="553f5-164">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="553f5-165">路由</span><span class="sxs-lookup"><span data-stu-id="553f5-165">Route</span></span>                            | <span data-ttu-id="553f5-166">Fragment</span><span class="sxs-lookup"><span data-stu-id="553f5-166">Fragment</span></span>             |
| -------------------------------- | -------------------- |
| `authentication/login`           | `<LoggingIn>`        |
| `authentication/login-callback`  | `<CompletingLogIn>`  |
| `authentication/login-failed`    | `<LogInFailed>`      |
| `authentication/logout`          | `<LoggingOut>`       |
| `authentication/logout-callback` | `<CompletingLogOut>` |
| `authentication/logout-failed`   | `<LogOutFailed>`     |
| `authentication/logged-out`      | `<LogOutSucceeded>`  |
| `authentication/profile`         | `<UserProfile>`      |
| `authentication/register`        | `<Registering>`      |
