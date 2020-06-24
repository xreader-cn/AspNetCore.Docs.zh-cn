---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: 了解如何为其他安全方案配置 Blazor WebAssembly。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 2020b422ad48a9c4c52f2670fd3b5054aa4d60c5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103225"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly 其他安全方案

作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>将令牌附加到传出请求

可以将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 服务与 <xref:System.Net.Http.HttpClient> 一起使用，以将访问令牌附加到传出请求。 令牌是使用现有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 服务获取的。 如果无法获取令牌，则会引发 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航至标识提供者以获取新令牌。 可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 方法将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 配置为授权的 URL、作用域和返回 URL。

使用以下两种方法之一为传出请求配置消息处理程序：

* [自定义 AuthorizationMessageHandler 类](#custom-authorizationmessagehandler-class)（推荐使用**）
* [配置 AuthorizationMessageHandler](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a>自定义 AuthorizationMessageHandler 类

在下面的示例中，自定义类扩展了可用于配置 <xref:System.Net.Http.HttpClient> 的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>：

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

在 `Program.Main`（Program.cs**）中，使用自定义授权消息处理程序配置 <xref:System.Net.Http.HttpClient>：

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

配置的 <xref:System.Net.Http.HttpClient> 用于使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求。 如果使用 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包）创建客户端的情况，则在向服务器 API 发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例：

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a>配置 AuthorizationMessageHandler

在下面的示例中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 在 `Program.Main` (Program.cs**) 中配置 <xref:System.Net.Http.HttpClient>：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

为了方便起见，包含 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>，它预先配置了应用基址作为授权的 URL。 支持身份验证的 Blazor WebAssembly 模板现在使用服务器 API 项目中的 <xref:System.Net.Http.IHttpClientFactory>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包），通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 设置 <xref:System.Net.Http.HttpClient>：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

如果使用上一示例中的 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> 创建客户端，则在向服务器项目发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例。

配置的 <xref:System.Net.Http.HttpClient> 用于使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求：

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a>类型化 HttpClient

可以定义类型化的客户端，以使用它处理单个类中的所有 HTTP 和令牌获取问题。

WeatherForecastClient.cs**：

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `using static BlazorSample.Data;`）。

`Program.Main` (*Program.cs*)：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData` component (*Pages/FetchData.razor*)：

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>配置 HttpClient 处理程序

可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求进一步配置处理程序。

`Program.Main` (*Program.cs*)：

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求

如果 Blazor WebAssembly 应用通常使用安全的默认 <xref:System.Net.Http.HttpClient>，则该应用还可以通过配置命名的 <xref:System.Net.Http.HttpClient> 来发出未经身份验证或未经授权的 Web API 请求：

`Program.Main` (*Program.cs*)：

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

先前的注册是对现有安全默认 <xref:System.Net.Http.HttpClient> 注册的补充。

组件从 <xref:System.Net.Http.IHttpClientFactory>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包）创建 <xref:System.Net.Http.HttpClient>，以发出未经身份验证或未经授权的请求：

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> 服务器 API 中的控制器（上述示例中为 `WeatherForecastNoAuthenticationController`）未标记 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。

## <a name="request-additional-access-tokens"></a>请求其他访问令牌

可以通过调用 `IAccessTokenProvider.RequestAccessToken` 手动获取访问令牌。

在以下示例中，应用需要其他 Azure Active Directory (AAD) Microsoft Graph API 作用域才能读取用户数据和发送邮件。 在 Azure AAD 门户中添加 Microsoft Graph API 权限后，将在客户端应用中配置其他作用域。

`Program.Main` (*Program.cs*)：

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

`IAccessTokenProvider.RequestToken` 方法提供重载，该重载允许应用使用一组给定的作用域来配置访问令牌。

在 Razor 组件中：

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：

* 如果使用 `token`，则为 `true`。
* 如果未检索到令牌，则为 `false`。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>具有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage

在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，可以使用 [HttpClient](xref:fundamentals/http-requests) 和 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。 例如，可以指定 HTTP 方法和请求标头。 以下组件向服务器上的待办事项列表 API 终结点发出 `POST` 请求，并显示响应正文：

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<xref:System.Net.Http.HttpClient> 的 .NET WebAssembly 实现使用 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。 提取允许配置多个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。 

使用下表中所示的 <xref:System.Net.Http.HttpRequestMessage> 扩展方法可配置 HTTP fetch 请求选项。

| 扩展方法 | 提取请求属性 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [凭据](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [缓存](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [模式](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [完整性](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

可使用更通用的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 扩展方法设置其他选项。
 
HTTP 响应通常在 Blazor WebAssembly 应用中缓冲，以支持同步读取响应内容。 要支持响应流式处理，请对请求使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 扩展方法。

要在跨源请求中加入凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

有关 Fetch API 选项的详细信息，请参阅 [MDN Web 文档：WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

在 CORS 请求中发送凭据（授权 cookie/标头）时，CORS 策略必须允许使用 `Authorization` 标头。

以下策略包括的配置用于：

* 请求来源（`http://localhost:5000`、`https://localhost:5001`）。
* Any 方法（谓词）。
* `Content-Type` 和 `Authorization` 标头。 若要允许使用自定义标头（例如 `x-custom-header`），请在调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 时列出标头。
* 由客户端 JavaScript 代码设置的凭据（`credentials` 属性设置为 `include`）。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件 (Components/HTTPRequestTester.razor**)。

## <a name="handle-token-request-errors"></a>处理令牌请求错误

当单页应用程序 (SPA) 使用 Open ID Connect (OIDC) 对用户进行身份验证时，身份验证状态将以会话 cookie（因用户提供其凭据而设置）的形式在 SPA 内和 Identity 提供者 (IP) 中在本地进行维护。

IP 为用户发出的令牌通常在短时间（约 1 小时）内有效，因此客户端应用必须定期提取新令牌。 否则，在授予的令牌到期后，将注销用户。 在大多数情况下，由于身份验证状态或“会话”保留在 IP 中，因此 OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。

在某些情况下，如果没有用户交互，客户端就无法获得令牌（例如，当用户出于某种原因而明确从 IP 注销时）。 如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些场景下，应用不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。 另外，当前令牌过期后，如果没有用户交互，客户端将无法预配新令牌。

这些场景并不特定于基于令牌的身份验证。 它们本就是 SPA 的一部分。 如果删除了身份验证 cookie，则使用 cookie 的 SPA 也无法调用服务器 API。

当应用对受保护的资源执行 API 调用时，请注意以下事项：

* 要预配新的访问令牌来调用 API，可能需要用户再次进行身份验证。
* 即使客户端拥有看似有效的令牌，对服务器的调用也可能会失败，因为该令牌已由用户撤销。

当应用请求令牌时，有两种可能的结果：

* 请求成功，应用拥有有效的令牌。
* 请求失败，应用必须再次验证用户身份才能获得新令牌。

当令牌请求失败时，需要决定在执行重定向之前是否要保存任何当前状态。 随着复杂程度的提高，可以使用以下几种方法：

* 将当前页面状态存储在会话存储中。 在 [OnInitializedAsync 生命周期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期间，检查状态是否可以还原，然后再继续执行操作。
* 添加查询字符串参数，并使用该参数向应用发送信号，而该应用需要重新水化先前保存的状态。
* 添加具有唯一标识符的查询字符串参数以将数据存储在会话存储中，而不会导致与其他项发生冲突。

以下示例介绍如何：

* 在重定向到登录页面之前保留状态。
* 使用查询字符串参数恢复先前经过身份验证后的状态。

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

## <a name="save-app-state-before-an-authentication-operation"></a>在身份验证操作之前保存应用状态

在身份验证操作过程中，有时要在将浏览器重定向到 IP 之前保存应用状态。 其中一种情况是使用状态容器并希望在身份验证成功后恢复状态。 可以使用自定义身份验证状态对象来保留特定于应用的状态或对其的引用，并在身份验证操作成功完成期间恢复该状态。 下面的示例演示了该方法。

在应用中创建状态容器类，该类具有用于保存应用的状态值的属性。 在以下示例中，容器用于维护默认模板的 `Counter` 组件 (Pages/Counter.razor**) 的计数器值。 用于序列化和反序列化容器的方法基于 <xref:System.Text.Json>。

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

`Counter` 组件使用状态容器来维护组件以外的 `currentCount` 值：

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 创建 `ApplicationAuthenticationState`。 提供 `Id` 属性，该属性用作本地存储状态的标识符。

ApplicationAuthenticationState.cs**：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication` 组件 (Pages/Authentication.razor**) 使用本地会话存储以及 `StateContainer` 序列化和反序列化方法（`GetStateForLocalStorage` 和 `SetStateFromLocalStorage`）保存和还原应用的状态：

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

本示例使用 Azure Active Directory (AAD) 进行身份验证。 在 `Program.Main` (Program.cs**) 中：

* 将 `ApplicationAuthenticationState` 配置为 Microsoft 身份验证库 (MSAL)`RemoteAuthenticationState` 类型。
* 在服务容器中注册状态容器。

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>自定义应用路由

默认情况下，[Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 库使用下表中显示的路由表示不同的身份验证状态。

| 路由                            | 目标 |
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

上表中显示的路由可通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 进行配置。 设置选项以提供自定义路由时，确认应用具有可处理每个路径的路由。

在以下示例中，所有路径都带有 `/security` 前缀。

`Authentication` 组件 (Pages/Authentication.razor**)：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*Program.cs*)：

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

如果需求要求使用完全不同的路径，请按照前面所述设置路由，并使用显式的操作参数呈现 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果选择这样做，则允许将 UI 分成不同的页面。

## <a name="customize-the-authentication-user-interface"></a>自定义身份验证用户界面

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 包括每个身份验证状态的一组默认 UI 片段。 可以通过传入自定义 <xref:Microsoft.AspNetCore.Components.RenderFragment> 自定义每个状态。 要在初始登录过程中自定义显示的文本，可以按如下所示更改 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>。

`Authentication` 组件 (Pages/Authentication.razor**)：

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

下表中显示了 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 的一个片段，该片段可用于每个身份验证路由。

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

## <a name="customize-the-user"></a>自定义用户

可自定义绑定到应用的用户。 在以下示例中，所有经过身份验证的用户都会收到每种用户身份验证方法的 `amr` 声明。

创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 类的类：

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 的中心：

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

为正在使用的验证提供程序注册 `CustomAccountFactory`。 以下任何注册均有效： 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>：

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>：

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>：

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

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

        builder.Services.AddTransient(new HttpClient 
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
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

在服务器应用的 `Startup.Configure` 方法中，将 [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 替换为 [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

在服务器应用中，如果不存在 Pages** 文件夹，则创建它。 在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。** ** 将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。** ** 更新文件的内容：

* 将 `@page "_Host"` 添加到文件顶部。
* 将 `<app>Loading...</app>` 标记替换为以下内容：

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
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

使用第三方登录提供程序配置 Identity。 获取第三方 API 访问所需的令牌并进行存储。

当用户登录时，Identity 将在身份验证过程中收集访问令牌和刷新令牌。 此时，可通过几种方法向第三方 API 进行 API 调用。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>使用服务器访问令牌检索第三方访问令牌

使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。 在此处，使用第三方访问令牌直接从客户端上的 Identity 调用第三方 API 资源。

我们不建议使用此方法。 此方法需要将第三方访问令牌视为针对公共客户端生成。 在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。 机密客户端具有客户端机密，并且假定能够安全地存储机密。

* 第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。
* 同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>从客户端向服务器 API 发出 API 调用以便调用第三方 API

从客户端向服务器 API 发出 API 调用。 从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。

尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：

* 服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。
* 应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>使用 pen ID Connect (OIDC) v2.0 终结点

身份验证库和 Blazor 模板使用 Open ID Connect (OIDC) v1.0 终结点。 要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。 在下面的示例中，通过向 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> 属性追加 `v2.0` 段，将 AAD 配置为 v2.0：

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

也可以在应用设置 (appsettings.json**) 文件中进行设置：

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。 使用 `Authority` 键在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 或应用设置文件 (appsettings.json**) 中设置属性。

ID 令牌中的声明列表针对 v2.0 终结点会发生更改。 有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。
