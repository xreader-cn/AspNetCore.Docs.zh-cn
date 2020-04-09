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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="c9f09-102">ASP.NET核心 Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="c9f09-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="c9f09-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c9f09-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="c9f09-104">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="c9f09-104">Request additional access tokens</span></span>

<span data-ttu-id="c9f09-105">大多数应用仅需要访问令牌才能与其使用的受保护资源进行交互。</span><span class="sxs-lookup"><span data-stu-id="c9f09-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="c9f09-106">在某些情况下，应用可能需要多个令牌才能与两个或多个资源进行交互。</span><span class="sxs-lookup"><span data-stu-id="c9f09-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="c9f09-107">在下面的示例中，应用需要其他 Azure 活动目录 （AAD） Microsoft 图形 API 作用域来读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="c9f09-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="c9f09-108">在 Azure AAD 门户中添加 Microsoft 图形 API 权限后，其他作用域在客户端应用`Program.Main`*（Program.cs）* 中配置。</span><span class="sxs-lookup"><span data-stu-id="c9f09-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="c9f09-109">该方法`IAccessTokenProvider.RequestToken`提供重载，允许应用使用一组给定的范围预配令牌，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="c9f09-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="c9f09-110">`TryGetToken`返回：</span><span class="sxs-lookup"><span data-stu-id="c9f09-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="c9f09-111">`true`与`token`使用。</span><span class="sxs-lookup"><span data-stu-id="c9f09-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="c9f09-112">`false`如果未检索令牌。</span><span class="sxs-lookup"><span data-stu-id="c9f09-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="c9f09-113">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="c9f09-113">Handle token request errors</span></span>

<span data-ttu-id="c9f09-114">当单页应用程序 （SPA） 使用开放 ID 连接 （OIDC） 对用户进行身份验证时，身份验证状态将在 SPA 中本地和标识提供程序 （IP） 中以会话 Cookie 的形式维护，该状态是用户提供凭据而设置的。</span><span class="sxs-lookup"><span data-stu-id="c9f09-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="c9f09-115">IP 为用户发出的令牌通常在短时间内有效，通常大约一小时，因此客户端应用必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="c9f09-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="c9f09-116">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="c9f09-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="c9f09-117">在大多数情况下，由于 IP 中的身份验证状态或"会话"，OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c9f09-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="c9f09-118">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户显式注销了 IP。</span><span class="sxs-lookup"><span data-stu-id="c9f09-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="c9f09-119">如果用户访问`https://login.microsoftonline.com`并注销，将发生此情况。在这些情况下，应用不会立即知道用户已注销。客户端持有的任何令牌可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="c9f09-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="c9f09-120">此外，在当前令牌过期后，客户端无法预配没有用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="c9f09-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="c9f09-121">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="c9f09-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="c9f09-122">它们是 SPA 性质的一部分。</span><span class="sxs-lookup"><span data-stu-id="c9f09-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="c9f09-123">如果删除身份验证 Cookie，则使用 Cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="c9f09-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="c9f09-124">当应用对受保护的资源执行 API 调用时，您必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="c9f09-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="c9f09-125">要预配新的访问令牌以调用 API，可能需要用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c9f09-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="c9f09-126">即使客户端具有看似有效的令牌，对服务器的调用也可能失败，因为令牌已被用户吊销。</span><span class="sxs-lookup"><span data-stu-id="c9f09-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="c9f09-127">当应用请求令牌时，有两种可能的结果：</span><span class="sxs-lookup"><span data-stu-id="c9f09-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="c9f09-128">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="c9f09-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="c9f09-129">请求失败，应用必须再次对用户进行身份验证才能获得新令牌。</span><span class="sxs-lookup"><span data-stu-id="c9f09-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="c9f09-130">当令牌请求失败时，您需要在执行重定向之前决定是否要保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="c9f09-131">存在几种方法，复杂性越来越高：</span><span class="sxs-lookup"><span data-stu-id="c9f09-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="c9f09-132">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="c9f09-132">Store the current page state in session storage.</span></span> <span data-ttu-id="c9f09-133">在`OnInitializeAsync`期间，检查是否可以在继续之前恢复状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="c9f09-134">添加查询字符串参数，并将其用作向应用发出信号，使其需要重新补充以前保存的状态的一种方式。</span><span class="sxs-lookup"><span data-stu-id="c9f09-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="c9f09-135">添加具有唯一标识符的查询字符串参数，将数据存储在会话存储中，而不会冒与其他项发生冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="c9f09-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="c9f09-136">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="c9f09-136">The following example shows how to:</span></span>

* <span data-ttu-id="c9f09-137">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="c9f09-138">使用查询字符串参数恢复以前的状态后身份验证。</span><span class="sxs-lookup"><span data-stu-id="c9f09-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="c9f09-139">在身份验证操作之前保存应用状态</span><span class="sxs-lookup"><span data-stu-id="c9f09-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="c9f09-140">在身份验证操作期间，在某些情况下，您希望在浏览器重定向到 IP 之前保存应用状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="c9f09-141">当您使用状态容器之类的内容，并且希望在身份验证成功后还原状态时，情况可能如此。</span><span class="sxs-lookup"><span data-stu-id="c9f09-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="c9f09-142">可以使用自定义身份验证状态对象保留特定于应用的状态或对它的引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="c9f09-143">`Authentication`组件 （*页/身份验证.razor）：*</span><span class="sxs-lookup"><span data-stu-id="c9f09-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="c9f09-144">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="c9f09-144">Customize app routes</span></span>

<span data-ttu-id="c9f09-145">默认情况下，`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="c9f09-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="c9f09-146">路由</span><span class="sxs-lookup"><span data-stu-id="c9f09-146">Route</span></span>                            | <span data-ttu-id="c9f09-147">目的</span><span class="sxs-lookup"><span data-stu-id="c9f09-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="c9f09-148">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="c9f09-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="c9f09-149">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="c9f09-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="c9f09-150">当登录操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c9f09-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="c9f09-151">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="c9f09-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="c9f09-152">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="c9f09-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="c9f09-153">当注销操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c9f09-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="c9f09-154">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="c9f09-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="c9f09-155">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="c9f09-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="c9f09-156">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="c9f09-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="c9f09-157">上表中显示的路由可通过`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`进行配置。</span><span class="sxs-lookup"><span data-stu-id="c9f09-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="c9f09-158">设置选项以提供自定义路由时，请确认应用具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="c9f09-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="c9f09-159">在下面的示例中，所有路径都预定了`/security`。</span><span class="sxs-lookup"><span data-stu-id="c9f09-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="c9f09-160">`Authentication`组件 （*页/身份验证.razor）：*</span><span class="sxs-lookup"><span data-stu-id="c9f09-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="c9f09-161">`Program.Main`*（Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="c9f09-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="c9f09-162">如果要求对完全不同的路径进行调用，请按照前面所述设置路由，并`RemoteAuthenticatorView`呈现显式操作参数：</span><span class="sxs-lookup"><span data-stu-id="c9f09-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="c9f09-163">如果选择这样做，则可以将 UI 分解为不同的页面。</span><span class="sxs-lookup"><span data-stu-id="c9f09-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="c9f09-164">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="c9f09-164">Customize the authentication user interface</span></span>

<span data-ttu-id="c9f09-165">`RemoteAuthenticatorView`包括每个身份验证状态的默认 UI 部分集。</span><span class="sxs-lookup"><span data-stu-id="c9f09-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="c9f09-166">每个状态都可以通过传入自定义`RenderFragment`来自定义。</span><span class="sxs-lookup"><span data-stu-id="c9f09-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="c9f09-167">要在初始登录过程中自定义显示的文本，可以按如下方式更改`RemoteAuthenticatorView`。</span><span class="sxs-lookup"><span data-stu-id="c9f09-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="c9f09-168">`Authentication`组件 （*页/身份验证.razor）：*</span><span class="sxs-lookup"><span data-stu-id="c9f09-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="c9f09-169">有`RemoteAuthenticatorView`一个片段，可以按下表中显示的每个身份验证路由使用。</span><span class="sxs-lookup"><span data-stu-id="c9f09-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="c9f09-170">路由</span><span class="sxs-lookup"><span data-stu-id="c9f09-170">Route</span></span>                            | <span data-ttu-id="c9f09-171">片段</span><span class="sxs-lookup"><span data-stu-id="c9f09-171">Fragment</span></span>                |
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
