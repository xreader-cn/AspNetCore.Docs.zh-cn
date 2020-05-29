---
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly 其他安全方案

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="attach-tokens-to-outgoing-requests"></a>将令牌附加到传出请求

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>服务可用于 <xref:System.Net.Http.HttpClient> 将访问令牌附加到传出请求。 使用现有服务获取令牌 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 。 如果无法获取令牌， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 则会引发。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>提供了一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航到标识提供程序以获取新令牌。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>使用方法，可以配置授权 url、范围和返回 URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 。

在下面的示例中， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> 在中配置 `Program.Main` （*Program.cs*）：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

为方便起见， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 将包含的应用程序基址预配置为授权 URL。 启用身份验证的 Blazor WebAssembly 模板现在 <xref:System.Net.Http.IHttpClientFactory> 在服务器 API 项目中使用，以使用设置 <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

在前面的示例中使用创建客户端的位置提供了在 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> 向服务器项目发出请求时提供访问令牌的实例。

然后，将使用配置的 <xref:System.Net.Http.HttpClient> 来使用简单的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)模式发出授权请求。

`FetchData` component (*Pages/FetchData.razor*)：

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a>类型化 HttpClient

可以定义一个类型化客户端，用于处理单个类中的所有 HTTP 和标记获取问题。

WeatherForecastClient.cs  ：

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

可以进一步 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求配置处理程序。

`Program.Main` (*Program.cs*)：

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求

如果 Blazor WebAssembly 应用程序通常使用安全默认值 <xref:System.Net.Http.HttpClient> ，则该应用程序还可以通过配置命名的来进行未经身份验证或未授权的 web API 请求 <xref:System.Net.Http.HttpClient> ：

`Program.Main` (*Program.cs*)：

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

除了现有的安全默认注册以外，以上注册也是如此 <xref:System.Net.Http.HttpClient> 。

组件从创建， <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 以发出未经身份验证或未授权的请求：

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
> 对于前面的示例，服务器 API 中的控制器 `WeatherForecastNoAuthenticationController` 未标记为 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。

## <a name="request-additional-access-tokens"></a>请求其他访问令牌

可以通过调用手动获取访问令牌 `IAccessTokenProvider.RequestAccessToken` 。

在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。 在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用中配置其他作用域。

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

`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用一组给定的范围设置访问令牌。

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>返回

* `true``token`供使用的。
* `false`如果未检索到标记，则为。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage

在 WebAssembly 应用程序的 WebAssembly 上运行时 Blazor ， [HttpClient](xref:fundamentals/http-requests)和 <xref:System.Net.Http.HttpRequestMessage> 可用于自定义请求。 例如，可以指定 HTTP 方法和请求标头。 以下组件向 `POST` 服务器上的待办事项列表 API 终结点发出请求，并显示响应正文：

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

.NET WebAssembly 的实现 <xref:System.Net.Http.HttpClient> 使用[WindowOrWorkerGlobalScope （）](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。 提取允许配置几个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。 

HTTP fetch 请求选项可配置 <xref:System.Net.Http.HttpRequestMessage> 如下表中所示的扩展方法。

| 扩展方法 | 提取请求属性 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [凭据](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [区](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [完整性](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

您可以使用更通用的扩展方法来设置其他选项 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 。
 
HTTP 响应通常在 Blazor WebAssembly 应用中进行缓冲，以支持对响应内容进行同步读取。 若要启用对响应流的支持，请 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 对请求使用扩展方法。

若要在跨域请求中包含凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

有关获取 API 选项的详细信息，请参阅[MDN web 文档： WindowOrWorkerGlobalScope （）:P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

在 CORS 请求上发送凭据（授权 cookie/标头）时， `Authorization` cors 策略必须允许标头。

以下策略包括的配置：

* 请求源（ `http://localhost:5000` 、 `https://localhost:5001` ）。
* 任何方法（谓词）。
* `Content-Type`和 `Authorization` 标头。 若要允许自定义标头（例如 `x-custom-header` ），请在调用时列出标头 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。
* 由客户端 JavaScript 代码设置的凭据（ `credentials` 属性设置为 `include` ）。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。

## <a name="handle-token-request-errors"></a>处理令牌请求错误

当单页面应用程序（SPA）使用 Open ID Connect （OIDC）对用户进行身份验证时，身份验证状态将 Identity 以会话 cookie （设置为用户提供凭据的用户的结果）的形式保留在 SPA 中的本地和提供程序（IP）中。

通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。 否则，用户将在授予的令牌过期后注销。 在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。

在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。 如果用户访问和注销，则会发生这种情况 `https://login.microsoftonline.com` 。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。 此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。

这些方案不特定于基于令牌的身份验证。 它们是 Spa 的性质组成部分。 如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。

当应用执行对受保护资源的 API 调用时，必须注意以下事项：

* 若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。
* 即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。

当应用请求令牌时，有两个可能的结果：

* 请求成功，并且应用具有有效的令牌。
* 请求失败，应用必须重新对用户进行身份验证才能获取新令牌。

如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。 增加复杂程度的方法有多种：

* 将当前页面状态存储在会话存储中。 在[OnInitializedAsync 生命周期事件](xref:blazor/lifecycle#component-initialization-methods)（ <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ）期间，请检查状态是否可以恢复，然后再继续。
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
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

默认情况下， [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)库使用下表中显示的路由来表示不同的身份验证状态。

| 路由                            | 目的 |
| ---
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---- | |`authentication/login`           |触发登录操作。 | |`authentication/login-callback`  |处理任何登录操作的结果。 | |`authentication/login-failed`    |当登录操作因某种原因失败时显示错误消息。 | |`authentication/logout`          |触发注销操作。 | |`authentication/logout-callback` |处理注销操作的结果。 | |`authentication/logout-failed`   |出于某些原因，在注销操作失败时显示错误消息。 | |`authentication/logged-out`      |指示用户已成功注销。 | |`authentication/profile`         |触发操作以编辑用户配置文件。 | |`authentication/register`        |触发操作以注册新用户。 |

可以通过配置上表中显示的路由 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 。 设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。

在下面的示例中，所有路径都以为前缀 `/security` 。

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

如果要求调用完全不同的路径，请按前面所述设置路由，并 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 使用显式操作参数呈现：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果你选择这样做，则允许你将 UI 分成不同的页面。

## <a name="customize-the-authentication-user-interface"></a>自定义身份验证用户界面

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>为每个身份验证状态包含一组默认的 UI 段。 可以通过传入自定义来自定义每个状态 <xref:Microsoft.AspNetCore.Components.RenderFragment> 。 若要在初始登录过程中自定义显示的文本，可以更改，如下所示 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 。

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>有一个可用于下表中所示的每个身份验证路由的片段。

| 路由                            | 片段                |
| ---
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a>自定义用户

绑定到应用的用户可以自定义。 在下面的示例中，所有经过身份验证的用户都 `amr` 将收到每个用户身份验证方法的声明。

创建扩展类的类 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ：

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

创建一个扩展的工厂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> ：

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

注册 `CustomAccountFactory` 要使用的身份验证提供程序。 以下任何注册都是有效的： 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

在服务器应用程序的 `Startup.Configure` 方法中，将[终结点替换为。带有终结点的 MapFallbackToFile （"index .html"）](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [。MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：

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

Identity使用第三方登录提供程序进行配置。 获取第三方 API 访问所需的令牌并进行存储。

当用户登录时，会在 Identity 身份验证过程中收集访问令牌和刷新令牌。 此时，可通过几种方法向第三方 API 进行 API 调用。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>使用服务器访问令牌检索第三方访问令牌

使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。 在该处，使用第三方访问令牌直接从客户端调用第三方 API 资源 Identity 。

我们不建议使用此方法。 此方法需要将第三方访问令牌视为针对公共客户端生成。 在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。 机密客户端具有客户端机密，并且假定能够安全地存储机密。

* 第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。
* 同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>从客户端向服务器 API 发出 API 调用以便调用第三方 API

从客户端向服务器 API 发出 API 调用。 从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。

尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：

* 服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。
* 应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>使用 Open ID Connect （OIDC） v2.0 终结点

身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。 若要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。 在下面的示例中，通过将一个段附加到属性，为 v2.0 配置了 AAD `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ：

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

或者，可以在应用设置（*appsettings*）文件中进行设置：

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接设置属性。 在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 应用程序设置文件（*appsettings*）中或用键设置属性 `Authority` 。

对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。 有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。
