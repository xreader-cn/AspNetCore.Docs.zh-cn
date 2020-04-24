---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111170"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly 其他安全方案

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> 本文中的指南适用于 ASP.NET Core 3.2 预览版4。 本主题将更新到2005年4月24日星期五的预览版5。

## <a name="request-additional-access-tokens"></a>请求其他访问令牌

大多数应用程序只需要一个访问令牌来与它们所使用的受保护资源交互。 在某些情况下，应用程序可能需要多个令牌才能与两个或更多个资源交互。

在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。 在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用（`Program.Main`， *Program.cs*）中配置其他范围：

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

`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用给定的范围集来预配标记，如以下示例中所示：

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

`TryGetToken`返回

* `true``token`供使用的。
* `false`如果未检索到标记，则为。

## <a name="attach-tokens-to-outgoing-requests"></a>将令牌附加到传出请求

`AuthorizationMessageHandler`服务可用于`HttpClient`将访问令牌附加到传出请求。 使用现有`IAccessTokenProvider`服务获取令牌。 如果无法获取令牌， `AccessTokenNotAvailableException`则会引发。 `AccessTokenNotAvailableException`提供了`Redirect`一个方法，该方法可用于将用户导航到标识提供程序以获取新令牌。 使用`AuthorizationMessageHandler` `ConfigureHandler`方法，可以配置授权 url、范围和返回 URL。

在下面的示例中`AuthorizationMessageHandler` ， `HttpClient`在中`Program.Main`配置（*Program.cs*）：

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

为方便起见， `BaseAddressAuthorizationMessageHandler`将包含的应用程序基址预配置为授权 URL。 启用身份验证的 Blazor WebAssembly 模板现在使用[IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) `HttpClient`通过`BaseAddressAuthorizationMessageHandler`以下方式设置：

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

在前面的示例中使用`CreateClient`创建客户端的位置提供`HttpClient`了在向服务器项目发出请求时提供访问令牌的实例。

然后， `HttpClient`将使用配置的来使用简单`try-catch`模式发出授权请求。 以下`FetchData`组件请求天气预测数据：

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

或者，您可以定义一个类型化的客户端，用于处理一个类中的所有 HTTP 和标记获取问题：

*WeatherClient.cs*：

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

Program.cs  :

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*FetchData*：

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a>处理令牌请求错误

在单页面应用程序（SPA）使用开放 ID Connect （OIDC）对用户进行身份验证时，身份验证状态以本地方式在 SPA 和标识提供程序（IP）中进行维护，其形式为用户提供其凭据的用户。

通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。 否则，用户将在授予的令牌过期后注销。 在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。

在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。 如果用户访问`https://login.microsoftonline.com`和注销，则会发生这种情况。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。 此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。

这些方案不特定于基于令牌的身份验证。 它们是 Spa 的性质组成部分。 如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。

当应用执行对受保护资源的 API 调用时，必须注意以下事项：

* 若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。
* 即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。

当应用请求令牌时，有两个可能的结果：

* 请求成功，并且应用具有有效的令牌。
* 请求失败，应用必须重新对用户进行身份验证才能获取新令牌。

如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。 增加复杂程度的方法有多种：

* 将当前页面状态存储在会话存储中。 在`OnInitializeAsync`期间，请检查是否可以在继续操作之前还原状态。
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
            await httpClient.PostAsJsonAsync("Save", User);
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

`Authentication`组件（*Pages/Authentication*）：

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

默认情况下， `Microsoft.AspNetCore.Components.WebAssembly.Authentication`该库使用下表中显示的路由来表示不同的身份验证状态。

| 路由                            | 用途 |
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

可以通过`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`配置上表中显示的路由。 设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。

在下面的示例中，所有路径都以`/security`为前缀。

`Authentication`组件（*Pages/Authentication*）：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`（*Program.cs*）：

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

如果要求调用完全不同的路径，请按前面所述设置路由，并`RemoteAuthenticatorView`使用显式操作参数呈现：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果你选择这样做，则允许你将 UI 分成不同的页面。

## <a name="customize-the-authentication-user-interface"></a>自定义身份验证用户界面

`RemoteAuthenticatorView`为每个身份验证状态包含一组默认的 UI 段。 可以通过传入自定义`RenderFragment`来自定义每个状态。 若要在初始登录过程中自定义显示的文本，可以`RemoteAuthenticatorView`更改，如下所示。

`Authentication`组件（*Pages/Authentication*）：

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

`RemoteAuthenticatorView`有一个可用于下表中所示的每个身份验证路由的片段。

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

## <a name="support-prerendering-with-authentication"></a>支持预呈现身份验证

遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：

* 预呈现不需要授权的路径。
* 不预呈现需要授权的路径。

在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

在服务器应用的 `Startup.Configure` 方法中，将 `endpoints.MapFallbackToFile("index.html")` 替换为 `endpoints.MapFallbackToPage("/_Host")`：

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

在服务器应用中，如果不存在 Pages  文件夹，则创建它。 在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。   将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。   更新文件的内容：

* 将 `@page "_Host"` 添加到文件顶部。
* 将 `<app>Loading...</app>` 标记替换为以下内容：

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>用于托管应用和第三方登录提供程序的选项

使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。 选择哪一种选项取决于方案。

有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>对用户进行身份验证，以仅调用受保护的第三方 API

使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 在本方案中：

* 托管应用的服务器不会发挥作用。
* 无法保护服务器上的 API。
* 应用只能调用受保护的第三方 API。

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API

使用第三方登录提供程序配置标识。 获取第三方 API 访问所需的令牌并进行存储。

当用户登录时，标识将在身份验证过程中收集访问和刷新令牌。 此时，可通过几种方法向第三方 API 进行 API 调用。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>使用服务器访问令牌检索第三方访问令牌

使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。 在此处，使用第三方访问令牌直接从客户端上的标识调用第三方 API 资源。

我们不建议使用此方法。 此方法需要将第三方访问令牌视为针对公共客户端生成。 在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。 机密客户端具有客户端机密，并且假定能够安全地存储机密。

* 第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。
* 同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>从客户端向服务器 API 发出 API 调用以便调用第三方 API

从客户端向服务器 API 发出 API 调用。 从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。

尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：

* 服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。
* 应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。
