---
<span data-ttu-id="c98e2-101">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-102">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-103">'Blazor'</span></span>
- <span data-ttu-id="c98e2-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-104">'Identity'</span></span>
- <span data-ttu-id="c98e2-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-106">'Razor'</span></span>
- <span data-ttu-id="c98e2-107">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="c98e2-108">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="c98e2-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="c98e2-109">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c98e2-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="c98e2-110">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="c98e2-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="c98e2-111">`AuthorizationMessageHandler`服务可用于 `HttpClient` 将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="c98e2-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="c98e2-112">使用现有服务获取令牌 `IAccessTokenProvider` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="c98e2-113">如果无法获取令牌， `AccessTokenNotAvailableException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="c98e2-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="c98e2-114">`AccessTokenNotAvailableException`提供了一个 `Redirect` 方法，该方法可用于将用户导航到标识提供程序以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="c98e2-115">`AuthorizationMessageHandler`使用方法，可以配置授权 url、范围和返回 URL `ConfigureHandler` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="c98e2-116">在下面的示例中， `AuthorizationMessageHandler` `HttpClient` 在中配置 `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="c98e2-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="c98e2-117">为方便起见， `BaseAddressAuthorizationMessageHandler` 将包含的应用程序基址预配置为授权 URL。</span><span class="sxs-lookup"><span data-stu-id="c98e2-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="c98e2-118">启用身份验证的 Blazor WebAssembly 模板现在 <xref:System.Net.Http.IHttpClientFactory> 在服务器 API 项目中使用，以使用设置 <xref:System.Net.Http.HttpClient> `BaseAddressAuthorizationMessageHandler` ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="c98e2-119">在前面的示例中使用创建客户端的位置提供了在 `CreateClient` <xref:System.Net.Http.HttpClient> 向服务器项目发出请求时提供访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="c98e2-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="c98e2-120">然后，将使用配置的 <xref:System.Net.Http.HttpClient> 来使用简单模式发出授权请求 `try-catch` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="c98e2-121">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="c98e2-122">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="c98e2-122">Typed HttpClient</span></span>

<span data-ttu-id="c98e2-123">可以定义一个类型化客户端，用于处理单个类中的所有 HTTP 和标记获取问题。</span><span class="sxs-lookup"><span data-stu-id="c98e2-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="c98e2-124">WeatherForecastClient.cs  ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="c98e2-125">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="c98e2-126">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="c98e2-127">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="c98e2-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="c98e2-128">可以进一步 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="c98e2-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="c98e2-129">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="c98e2-130">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="c98e2-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="c98e2-131">如果 Blazor WebAssembly 应用程序通常使用安全默认值 <xref:System.Net.Http.HttpClient> ，则该应用程序还可以通过配置命名的来进行未经身份验证或未授权的 web API 请求 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="c98e2-132">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="c98e2-133">除了现有的安全默认注册以外，以上注册也是如此 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="c98e2-134">组件从创建， <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 以发出未经身份验证或未授权的请求：</span><span class="sxs-lookup"><span data-stu-id="c98e2-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="c98e2-135">对于前面的示例，服务器 API 中的控制器 `WeatherForecastNoAuthenticationController` 未标记为 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="c98e2-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="c98e2-136">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="c98e2-136">Request additional access tokens</span></span>

<span data-ttu-id="c98e2-137">可以通过调用手动获取访问令牌 `IAccessTokenProvider.RequestAccessToken` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="c98e2-138">在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="c98e2-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="c98e2-139">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="c98e2-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="c98e2-140">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="c98e2-141">`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用一组给定的范围设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="c98e2-142">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="c98e2-142">In a Razor component:</span></span>

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

<span data-ttu-id="c98e2-143">`TryGetToken`返回</span><span class="sxs-lookup"><span data-stu-id="c98e2-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="c98e2-144">`true``token`供使用的。</span><span class="sxs-lookup"><span data-stu-id="c98e2-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="c98e2-145">`false`如果未检索到标记，则为。</span><span class="sxs-lookup"><span data-stu-id="c98e2-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="c98e2-146">带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="c98e2-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="c98e2-147">在 WebAssembly 应用程序的 WebAssembly 上运行时 Blazor ， [HttpClient](xref:fundamentals/http-requests)和 <xref:System.Net.Http.HttpRequestMessage> 可用于自定义请求。</span><span class="sxs-lookup"><span data-stu-id="c98e2-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="c98e2-148">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="c98e2-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="c98e2-149">以下组件向 `POST` 服务器上的待办事项列表 API 终结点发出请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="c98e2-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="c98e2-150">.NET WebAssembly 的实现 `HttpClient` 使用[WindowOrWorkerGlobalScope （）](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="c98e2-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="c98e2-151">提取允许配置几个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="c98e2-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="c98e2-152">HTTP fetch 请求选项可配置 `HttpRequestMessage` 如下表中所示的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c98e2-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="c98e2-153">`HttpRequestMessage`extension 方法</span><span class="sxs-lookup"><span data-stu-id="c98e2-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="c98e2-154">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="c98e2-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="c98e2-155">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-156">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-157">'Blazor'</span></span>
- <span data-ttu-id="c98e2-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-158">'Identity'</span></span>
- <span data-ttu-id="c98e2-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-160">'Razor'</span></span>
- <span data-ttu-id="c98e2-161">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-162">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-163">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-164">'Blazor'</span></span>
- <span data-ttu-id="c98e2-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-165">'Identity'</span></span>
- <span data-ttu-id="c98e2-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-167">'Razor'</span></span>
- <span data-ttu-id="c98e2-168">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-169">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-170">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-171">'Blazor'</span></span>
- <span data-ttu-id="c98e2-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-172">'Identity'</span></span>
- <span data-ttu-id="c98e2-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-174">'Razor'</span></span>
- <span data-ttu-id="c98e2-175">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-176">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-177">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-178">'Blazor'</span></span>
- <span data-ttu-id="c98e2-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-179">'Identity'</span></span>
- <span data-ttu-id="c98e2-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-181">'Razor'</span></span>
- <span data-ttu-id="c98e2-182">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-183">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-184">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-185">'Blazor'</span></span>
- <span data-ttu-id="c98e2-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-186">'Identity'</span></span>
- <span data-ttu-id="c98e2-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-188">'Razor'</span></span>
- <span data-ttu-id="c98e2-189">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-190">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-191">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-192">'Blazor'</span></span>
- <span data-ttu-id="c98e2-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-193">'Identity'</span></span>
- <span data-ttu-id="c98e2-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-195">'Razor'</span></span>
- <span data-ttu-id="c98e2-196">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-197">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-198">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-199">'Blazor'</span></span>
- <span data-ttu-id="c98e2-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-200">'Identity'</span></span>
- <span data-ttu-id="c98e2-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-202">'Razor'</span></span>
- <span data-ttu-id="c98e2-203">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-204">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-205">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-206">'Blazor'</span></span>
- <span data-ttu-id="c98e2-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-207">'Identity'</span></span>
- <span data-ttu-id="c98e2-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-209">'Razor'</span></span>
- <span data-ttu-id="c98e2-210">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-211">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-212">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-213">'Blazor'</span></span>
- <span data-ttu-id="c98e2-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-214">'Identity'</span></span>
- <span data-ttu-id="c98e2-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-216">'Razor'</span></span>
- <span data-ttu-id="c98e2-217">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-218">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-219">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-220">'Blazor'</span></span>
- <span data-ttu-id="c98e2-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-221">'Identity'</span></span>
- <span data-ttu-id="c98e2-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-223">'Razor'</span></span>
- <span data-ttu-id="c98e2-224">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-225">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-226">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-227">'Blazor'</span></span>
- <span data-ttu-id="c98e2-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-228">'Identity'</span></span>
- <span data-ttu-id="c98e2-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-230">'Razor'</span></span>
- <span data-ttu-id="c98e2-231">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-232">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-233">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-234">'Blazor'</span></span>
- <span data-ttu-id="c98e2-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-235">'Identity'</span></span>
- <span data-ttu-id="c98e2-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-237">'Razor'</span></span>
- <span data-ttu-id="c98e2-238">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-239">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-240">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-241">'Blazor'</span></span>
- <span data-ttu-id="c98e2-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-242">'Identity'</span></span>
- <span data-ttu-id="c98e2-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-244">'Razor'</span></span>
- <span data-ttu-id="c98e2-245">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-246">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-247">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-248">'Blazor'</span></span>
- <span data-ttu-id="c98e2-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-249">'Identity'</span></span>
- <span data-ttu-id="c98e2-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-251">'Razor'</span></span>
- <span data-ttu-id="c98e2-252">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-253">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-254">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-255">'Blazor'</span></span>
- <span data-ttu-id="c98e2-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-256">'Identity'</span></span>
- <span data-ttu-id="c98e2-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-258">'Razor'</span></span>
- <span data-ttu-id="c98e2-259">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-260">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-261">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-262">'Blazor'</span></span>
- <span data-ttu-id="c98e2-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-263">'Identity'</span></span>
- <span data-ttu-id="c98e2-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-265">'Razor'</span></span>
- <span data-ttu-id="c98e2-266">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-266">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-267">------------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-268">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-269">'Blazor'</span></span>
- <span data-ttu-id="c98e2-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-270">'Identity'</span></span>
- <span data-ttu-id="c98e2-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-272">'Razor'</span></span>
- <span data-ttu-id="c98e2-273">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-274">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-275">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-276">'Blazor'</span></span>
- <span data-ttu-id="c98e2-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-277">'Identity'</span></span>
- <span data-ttu-id="c98e2-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-279">'Razor'</span></span>
- <span data-ttu-id="c98e2-280">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-281">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-282">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-283">'Blazor'</span></span>
- <span data-ttu-id="c98e2-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-284">'Identity'</span></span>
- <span data-ttu-id="c98e2-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-286">'Razor'</span></span>
- <span data-ttu-id="c98e2-287">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-288">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-289">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-290">'Blazor'</span></span>
- <span data-ttu-id="c98e2-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-291">'Identity'</span></span>
- <span data-ttu-id="c98e2-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-293">'Razor'</span></span>
- <span data-ttu-id="c98e2-294">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-295">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-296">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-297">'Blazor'</span></span>
- <span data-ttu-id="c98e2-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-298">'Identity'</span></span>
- <span data-ttu-id="c98e2-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-300">'Razor'</span></span>
- <span data-ttu-id="c98e2-301">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-302">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-303">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-304">'Blazor'</span></span>
- <span data-ttu-id="c98e2-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-305">'Identity'</span></span>
- <span data-ttu-id="c98e2-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-307">'Razor'</span></span>
- <span data-ttu-id="c98e2-308">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-309">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-310">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-311">'Blazor'</span></span>
- <span data-ttu-id="c98e2-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-312">'Identity'</span></span>
- <span data-ttu-id="c98e2-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-314">'Razor'</span></span>
- <span data-ttu-id="c98e2-315">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-316">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-317">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-318">'Blazor'</span></span>
- <span data-ttu-id="c98e2-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-319">'Identity'</span></span>
- <span data-ttu-id="c98e2-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-321">'Razor'</span></span>
- <span data-ttu-id="c98e2-322">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-323">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-324">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-325">'Blazor'</span></span>
- <span data-ttu-id="c98e2-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-326">'Identity'</span></span>
- <span data-ttu-id="c98e2-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-328">'Razor'</span></span>
- <span data-ttu-id="c98e2-329">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-329">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-330">----------- | |`SetBrowserRequestCredentials`         |  [凭据](https://developer.mozilla.org/docs/Web/API/Request/credentials)| `SetBrowserRequestCache` |               | [缓存](https://developer.mozilla.org/docs/Web/API/Request/cache)| |`SetBrowserRequestMode`                |  [模式](https://developer.mozilla.org/docs/Web/API/Request/mode)| `SetBrowserRequestIntegrity` |           | [完整性](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="c98e2-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="c98e2-331">您可以使用更通用的扩展方法来设置其他选项 `SetBrowserRequestOption` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="c98e2-332">HTTP 响应通常在 Blazor WebAssembly 应用中进行缓冲，以支持对响应内容进行同步读取。</span><span class="sxs-lookup"><span data-stu-id="c98e2-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="c98e2-333">若要启用对响应流的支持，请 `SetBrowserResponseStreamingEnabled` 对请求使用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c98e2-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="c98e2-334">若要在跨域请求中包含凭据，请使用 `SetBrowserRequestCredentials` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c98e2-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="c98e2-335">有关获取 API 选项的详细信息，请参阅[MDN web 文档： WindowOrWorkerGlobalScope （）:P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="c98e2-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="c98e2-336">在 CORS 请求上发送凭据（授权 cookie/标头）时， `Authorization` cors 策略必须允许标头。</span><span class="sxs-lookup"><span data-stu-id="c98e2-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="c98e2-337">以下策略包括的配置：</span><span class="sxs-lookup"><span data-stu-id="c98e2-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="c98e2-338">请求源（ `http://localhost:5000` 、 `https://localhost:5001` ）。</span><span class="sxs-lookup"><span data-stu-id="c98e2-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="c98e2-339">任何方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="c98e2-339">Any method (verb).</span></span>
* <span data-ttu-id="c98e2-340">`Content-Type`和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="c98e2-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="c98e2-341">若要允许自定义标头（例如 `x-custom-header` ），请在调用时列出标头 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="c98e2-342">由客户端 JavaScript 代码设置的凭据（ `credentials` 属性设置为 `include` ）。</span><span class="sxs-lookup"><span data-stu-id="c98e2-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="c98e2-343">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。</span><span class="sxs-lookup"><span data-stu-id="c98e2-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="c98e2-344">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="c98e2-344">Handle token request errors</span></span>

<span data-ttu-id="c98e2-345">当单页面应用程序（SPA）使用 Open ID Connect （OIDC）对用户进行身份验证时，身份验证状态将 Identity 以会话 cookie （设置为用户提供凭据的用户的结果）的形式保留在 SPA 中的本地和提供程序（IP）中。</span><span class="sxs-lookup"><span data-stu-id="c98e2-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="c98e2-346">通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="c98e2-347">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="c98e2-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="c98e2-348">在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。</span><span class="sxs-lookup"><span data-stu-id="c98e2-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="c98e2-349">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。</span><span class="sxs-lookup"><span data-stu-id="c98e2-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="c98e2-350">如果用户访问和注销，则会发生这种情况 `https://login.microsoftonline.com` 。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="c98e2-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="c98e2-351">此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="c98e2-352">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="c98e2-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="c98e2-353">它们是 Spa 的性质组成部分。</span><span class="sxs-lookup"><span data-stu-id="c98e2-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="c98e2-354">如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="c98e2-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="c98e2-355">当应用执行对受保护资源的 API 调用时，必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="c98e2-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="c98e2-356">若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c98e2-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="c98e2-357">即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。</span><span class="sxs-lookup"><span data-stu-id="c98e2-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="c98e2-358">当应用请求令牌时，有两个可能的结果：</span><span class="sxs-lookup"><span data-stu-id="c98e2-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="c98e2-359">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="c98e2-360">请求失败，应用必须重新对用户进行身份验证才能获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="c98e2-361">如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="c98e2-362">增加复杂程度的方法有多种：</span><span class="sxs-lookup"><span data-stu-id="c98e2-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="c98e2-363">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="c98e2-363">Store the current page state in session storage.</span></span> <span data-ttu-id="c98e2-364">在期间 `OnInitializeAsync` ，请检查是否可以在继续操作之前还原状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="c98e2-365">添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="c98e2-366">添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="c98e2-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="c98e2-367">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="c98e2-367">The following example shows how to:</span></span>

* <span data-ttu-id="c98e2-368">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="c98e2-369">使用查询字符串参数恢复先前状态身份验证。</span><span class="sxs-lookup"><span data-stu-id="c98e2-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="c98e2-370">在身份验证操作之前保存应用程序状态</span><span class="sxs-lookup"><span data-stu-id="c98e2-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="c98e2-371">在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="c98e2-372">当使用类似于状态容器的内容时，如果你想要在身份验证成功后还原状态，则可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="c98e2-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="c98e2-373">可以使用自定义身份验证状态对象来保持应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="c98e2-374">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="c98e2-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="c98e2-375">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="c98e2-375">Customize app routes</span></span>

<span data-ttu-id="c98e2-376">默认情况下， `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 该库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="c98e2-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="c98e2-377">路由</span><span class="sxs-lookup"><span data-stu-id="c98e2-377">Route</span></span>                            | <span data-ttu-id="c98e2-378">目标</span><span class="sxs-lookup"><span data-stu-id="c98e2-378">Purpose</span></span> |
| ---
<span data-ttu-id="c98e2-379">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-380">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-381">'Blazor'</span></span>
- <span data-ttu-id="c98e2-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-382">'Identity'</span></span>
- <span data-ttu-id="c98e2-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-384">'Razor'</span></span>
- <span data-ttu-id="c98e2-385">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-386">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-387">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-388">'Blazor'</span></span>
- <span data-ttu-id="c98e2-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-389">'Identity'</span></span>
- <span data-ttu-id="c98e2-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-391">'Razor'</span></span>
- <span data-ttu-id="c98e2-392">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-393">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-394">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-395">'Blazor'</span></span>
- <span data-ttu-id="c98e2-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-396">'Identity'</span></span>
- <span data-ttu-id="c98e2-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-398">'Razor'</span></span>
- <span data-ttu-id="c98e2-399">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-400">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-401">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-402">'Blazor'</span></span>
- <span data-ttu-id="c98e2-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-403">'Identity'</span></span>
- <span data-ttu-id="c98e2-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-405">'Razor'</span></span>
- <span data-ttu-id="c98e2-406">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-407">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-408">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-409">'Blazor'</span></span>
- <span data-ttu-id="c98e2-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-410">'Identity'</span></span>
- <span data-ttu-id="c98e2-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-412">'Razor'</span></span>
- <span data-ttu-id="c98e2-413">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-414">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-415">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-416">'Blazor'</span></span>
- <span data-ttu-id="c98e2-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-417">'Identity'</span></span>
- <span data-ttu-id="c98e2-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-419">'Razor'</span></span>
- <span data-ttu-id="c98e2-420">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-421">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-422">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-423">'Blazor'</span></span>
- <span data-ttu-id="c98e2-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-424">'Identity'</span></span>
- <span data-ttu-id="c98e2-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-426">'Razor'</span></span>
- <span data-ttu-id="c98e2-427">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-428">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-429">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-430">'Blazor'</span></span>
- <span data-ttu-id="c98e2-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-431">'Identity'</span></span>
- <span data-ttu-id="c98e2-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-433">'Razor'</span></span>
- <span data-ttu-id="c98e2-434">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-435">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-436">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-437">'Blazor'</span></span>
- <span data-ttu-id="c98e2-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-438">'Identity'</span></span>
- <span data-ttu-id="c98e2-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-440">'Razor'</span></span>
- <span data-ttu-id="c98e2-441">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-442">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-443">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-444">'Blazor'</span></span>
- <span data-ttu-id="c98e2-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-445">'Identity'</span></span>
- <span data-ttu-id="c98e2-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-447">'Razor'</span></span>
- <span data-ttu-id="c98e2-448">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-449">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-450">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-451">'Blazor'</span></span>
- <span data-ttu-id="c98e2-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-452">'Identity'</span></span>
- <span data-ttu-id="c98e2-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-454">'Razor'</span></span>
- <span data-ttu-id="c98e2-455">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-456">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-457">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-458">'Blazor'</span></span>
- <span data-ttu-id="c98e2-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-459">'Identity'</span></span>
- <span data-ttu-id="c98e2-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-461">'Razor'</span></span>
- <span data-ttu-id="c98e2-462">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-463">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-464">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-465">'Blazor'</span></span>
- <span data-ttu-id="c98e2-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-466">'Identity'</span></span>
- <span data-ttu-id="c98e2-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-468">'Razor'</span></span>
- <span data-ttu-id="c98e2-469">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-470">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-471">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-472">'Blazor'</span></span>
- <span data-ttu-id="c98e2-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-473">'Identity'</span></span>
- <span data-ttu-id="c98e2-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-475">'Razor'</span></span>
- <span data-ttu-id="c98e2-476">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-476">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-477">---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-478">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-479">'Blazor'</span></span>
- <span data-ttu-id="c98e2-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-480">'Identity'</span></span>
- <span data-ttu-id="c98e2-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-482">'Razor'</span></span>
- <span data-ttu-id="c98e2-483">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-483">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-484">---- | |`authentication/login`           |触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="c98e2-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="c98e2-485">| |`authentication/login-callback`  |处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="c98e2-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="c98e2-486">| |`authentication/login-failed`    |当登录操作因某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c98e2-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="c98e2-487">| |`authentication/logout`          |触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="c98e2-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="c98e2-488">| |`authentication/logout-callback` |处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="c98e2-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="c98e2-489">| |`authentication/logout-failed`   |出于某些原因，在注销操作失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c98e2-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="c98e2-490">| |`authentication/logged-out`      |指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="c98e2-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="c98e2-491">| |`authentication/profile`         |触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="c98e2-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="c98e2-492">| |`authentication/register`        |触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="c98e2-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="c98e2-493">可以通过配置上表中显示的路由 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="c98e2-494">设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="c98e2-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="c98e2-495">在下面的示例中，所有路径都以为前缀 `/security` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="c98e2-496">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="c98e2-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="c98e2-497">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c98e2-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="c98e2-498">如果要求调用完全不同的路径，请按前面所述设置路由，并 `RemoteAuthenticatorView` 使用显式操作参数呈现：</span><span class="sxs-lookup"><span data-stu-id="c98e2-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="c98e2-499">如果你选择这样做，则允许你将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="c98e2-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="c98e2-500">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="c98e2-500">Customize the authentication user interface</span></span>

<span data-ttu-id="c98e2-501">`RemoteAuthenticatorView`为每个身份验证状态包含一组默认的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="c98e2-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="c98e2-502">可以通过传入自定义来自定义每个状态 `RenderFragment` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="c98e2-503">若要在初始登录过程中自定义显示的文本，可以更改，如下所示 `RemoteAuthenticatorView` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="c98e2-504">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="c98e2-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="c98e2-505">`RemoteAuthenticatorView`有一个可用于下表中所示的每个身份验证路由的片段。</span><span class="sxs-lookup"><span data-stu-id="c98e2-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="c98e2-506">路由</span><span class="sxs-lookup"><span data-stu-id="c98e2-506">Route</span></span>                            | <span data-ttu-id="c98e2-507">片段</span><span class="sxs-lookup"><span data-stu-id="c98e2-507">Fragment</span></span>                |
| ---
<span data-ttu-id="c98e2-508">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-509">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-510">'Blazor'</span></span>
- <span data-ttu-id="c98e2-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-511">'Identity'</span></span>
- <span data-ttu-id="c98e2-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-513">'Razor'</span></span>
- <span data-ttu-id="c98e2-514">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-515">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-516">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-517">'Blazor'</span></span>
- <span data-ttu-id="c98e2-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-518">'Identity'</span></span>
- <span data-ttu-id="c98e2-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-520">'Razor'</span></span>
- <span data-ttu-id="c98e2-521">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-522">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-523">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-524">'Blazor'</span></span>
- <span data-ttu-id="c98e2-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-525">'Identity'</span></span>
- <span data-ttu-id="c98e2-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-527">'Razor'</span></span>
- <span data-ttu-id="c98e2-528">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-529">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-530">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-531">'Blazor'</span></span>
- <span data-ttu-id="c98e2-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-532">'Identity'</span></span>
- <span data-ttu-id="c98e2-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-534">'Razor'</span></span>
- <span data-ttu-id="c98e2-535">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-536">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-537">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-538">'Blazor'</span></span>
- <span data-ttu-id="c98e2-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-539">'Identity'</span></span>
- <span data-ttu-id="c98e2-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-541">'Razor'</span></span>
- <span data-ttu-id="c98e2-542">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-543">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-544">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-545">'Blazor'</span></span>
- <span data-ttu-id="c98e2-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-546">'Identity'</span></span>
- <span data-ttu-id="c98e2-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-548">'Razor'</span></span>
- <span data-ttu-id="c98e2-549">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-550">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-551">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-552">'Blazor'</span></span>
- <span data-ttu-id="c98e2-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-553">'Identity'</span></span>
- <span data-ttu-id="c98e2-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-555">'Razor'</span></span>
- <span data-ttu-id="c98e2-556">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-557">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-558">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-559">'Blazor'</span></span>
- <span data-ttu-id="c98e2-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-560">'Identity'</span></span>
- <span data-ttu-id="c98e2-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-562">'Razor'</span></span>
- <span data-ttu-id="c98e2-563">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-564">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-565">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-566">'Blazor'</span></span>
- <span data-ttu-id="c98e2-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-567">'Identity'</span></span>
- <span data-ttu-id="c98e2-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-569">'Razor'</span></span>
- <span data-ttu-id="c98e2-570">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-571">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-572">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-573">'Blazor'</span></span>
- <span data-ttu-id="c98e2-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-574">'Identity'</span></span>
- <span data-ttu-id="c98e2-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-576">'Razor'</span></span>
- <span data-ttu-id="c98e2-577">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-578">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-579">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-580">'Blazor'</span></span>
- <span data-ttu-id="c98e2-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-581">'Identity'</span></span>
- <span data-ttu-id="c98e2-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-583">'Razor'</span></span>
- <span data-ttu-id="c98e2-584">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-585">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-586">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-587">'Blazor'</span></span>
- <span data-ttu-id="c98e2-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-588">'Identity'</span></span>
- <span data-ttu-id="c98e2-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-590">'Razor'</span></span>
- <span data-ttu-id="c98e2-591">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-592">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-593">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-594">'Blazor'</span></span>
- <span data-ttu-id="c98e2-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-595">'Identity'</span></span>
- <span data-ttu-id="c98e2-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-597">'Razor'</span></span>
- <span data-ttu-id="c98e2-598">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-599">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-600">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-601">'Blazor'</span></span>
- <span data-ttu-id="c98e2-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-602">'Identity'</span></span>
- <span data-ttu-id="c98e2-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-604">'Razor'</span></span>
- <span data-ttu-id="c98e2-605">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-605">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-606">---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-607">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-608">'Blazor'</span></span>
- <span data-ttu-id="c98e2-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-609">'Identity'</span></span>
- <span data-ttu-id="c98e2-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-611">'Razor'</span></span>
- <span data-ttu-id="c98e2-612">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-613">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-614">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-615">'Blazor'</span></span>
- <span data-ttu-id="c98e2-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-616">'Identity'</span></span>
- <span data-ttu-id="c98e2-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-618">'Razor'</span></span>
- <span data-ttu-id="c98e2-619">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-620">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-621">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-622">'Blazor'</span></span>
- <span data-ttu-id="c98e2-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-623">'Identity'</span></span>
- <span data-ttu-id="c98e2-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-625">'Razor'</span></span>
- <span data-ttu-id="c98e2-626">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-627">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-628">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-629">'Blazor'</span></span>
- <span data-ttu-id="c98e2-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-630">'Identity'</span></span>
- <span data-ttu-id="c98e2-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-632">'Razor'</span></span>
- <span data-ttu-id="c98e2-633">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-634">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-635">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-636">'Blazor'</span></span>
- <span data-ttu-id="c98e2-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-637">'Identity'</span></span>
- <span data-ttu-id="c98e2-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-639">'Razor'</span></span>
- <span data-ttu-id="c98e2-640">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-641">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-642">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-643">'Blazor'</span></span>
- <span data-ttu-id="c98e2-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-644">'Identity'</span></span>
- <span data-ttu-id="c98e2-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-646">'Razor'</span></span>
- <span data-ttu-id="c98e2-647">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-648">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-649">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-650">'Blazor'</span></span>
- <span data-ttu-id="c98e2-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-651">'Identity'</span></span>
- <span data-ttu-id="c98e2-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-653">'Razor'</span></span>
- <span data-ttu-id="c98e2-654">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-655">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-656">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-657">'Blazor'</span></span>
- <span data-ttu-id="c98e2-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-658">'Identity'</span></span>
- <span data-ttu-id="c98e2-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-660">'Razor'</span></span>
- <span data-ttu-id="c98e2-661">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c98e2-662">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="c98e2-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="c98e2-663">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="c98e2-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c98e2-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-664">'Blazor'</span></span>
- <span data-ttu-id="c98e2-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c98e2-665">'Identity'</span></span>
- <span data-ttu-id="c98e2-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c98e2-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="c98e2-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c98e2-667">'Razor'</span></span>
- <span data-ttu-id="c98e2-668">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="c98e2-668">'SignalR' uid:</span></span> 

<span data-ttu-id="c98e2-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="c98e2-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="c98e2-670">自定义用户</span><span class="sxs-lookup"><span data-stu-id="c98e2-670">Customize the user</span></span>

<span data-ttu-id="c98e2-671">绑定到应用的用户可以自定义。</span><span class="sxs-lookup"><span data-stu-id="c98e2-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="c98e2-672">在下面的示例中，所有经过身份验证的用户都 `amr` 将收到每个用户身份验证方法的声明。</span><span class="sxs-lookup"><span data-stu-id="c98e2-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="c98e2-673">创建扩展类的类 `RemoteUserAccount` ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="c98e2-674">创建一个扩展的工厂 `AccountClaimsPrincipalFactory<TAccount>` ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="c98e2-675">注册 `CustomAccountFactory` 要使用的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="c98e2-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="c98e2-676">以下任何注册都是有效的：</span><span class="sxs-lookup"><span data-stu-id="c98e2-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="c98e2-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="c98e2-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="c98e2-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="c98e2-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="c98e2-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="c98e2-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="c98e2-680">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="c98e2-680">Support prerendering with authentication</span></span>

<span data-ttu-id="c98e2-681">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="c98e2-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="c98e2-682">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="c98e2-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="c98e2-683">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="c98e2-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="c98e2-684">在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="c98e2-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="c98e2-685">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="c98e2-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="c98e2-686">在服务器应用的 `Startup.Configure` 方法中，将 `endpoints.MapFallbackToFile("index.html")` 替换为 `endpoints.MapFallbackToPage("/_Host")`：</span><span class="sxs-lookup"><span data-stu-id="c98e2-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="c98e2-687">在服务器应用中，如果不存在 Pages  文件夹，则创建它。</span><span class="sxs-lookup"><span data-stu-id="c98e2-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="c98e2-688">在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。  </span><span class="sxs-lookup"><span data-stu-id="c98e2-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="c98e2-689">将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。  </span><span class="sxs-lookup"><span data-stu-id="c98e2-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="c98e2-690">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="c98e2-690">Update the file's contents:</span></span>

* <span data-ttu-id="c98e2-691">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="c98e2-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="c98e2-692">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="c98e2-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="c98e2-693">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="c98e2-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="c98e2-694">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c98e2-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="c98e2-695">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="c98e2-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="c98e2-696">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="c98e2-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="c98e2-697">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="c98e2-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="c98e2-698">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="c98e2-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="c98e2-699">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="c98e2-699">In this scenario:</span></span>

* <span data-ttu-id="c98e2-700">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="c98e2-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="c98e2-701">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="c98e2-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="c98e2-702">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="c98e2-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="c98e2-703">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="c98e2-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="c98e2-704">Identity使用第三方登录提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="c98e2-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="c98e2-705">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="c98e2-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="c98e2-706">当用户登录时，会在 Identity 身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="c98e2-707">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="c98e2-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="c98e2-708">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="c98e2-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="c98e2-709">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="c98e2-710">在该处，使用第三方访问令牌直接从客户端调用第三方 API 资源 Identity 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="c98e2-711">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="c98e2-711">We don't recommend this approach.</span></span> <span data-ttu-id="c98e2-712">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="c98e2-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="c98e2-713">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="c98e2-714">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="c98e2-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="c98e2-715">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="c98e2-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="c98e2-716">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="c98e2-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="c98e2-717">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="c98e2-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="c98e2-718">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="c98e2-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="c98e2-719">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="c98e2-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="c98e2-720">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="c98e2-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="c98e2-721">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="c98e2-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="c98e2-722">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c98e2-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="c98e2-723">使用 Open ID Connect （OIDC） v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="c98e2-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="c98e2-724">身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="c98e2-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="c98e2-725">若要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="c98e2-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="c98e2-726">在下面的示例中，通过将一个段附加到属性，为 v2.0 配置了 AAD `v2.0` `Authority` ：</span><span class="sxs-lookup"><span data-stu-id="c98e2-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="c98e2-727">或者，可以在应用设置（*appsettings*）文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="c98e2-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="c98e2-728">如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 `Authority` 直接设置属性。</span><span class="sxs-lookup"><span data-stu-id="c98e2-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="c98e2-729">在 `JwtBearerOptions` 应用设置文件中或在应用设置文件中用键设置属性 `Authority` 。</span><span class="sxs-lookup"><span data-stu-id="c98e2-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="c98e2-730">对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。</span><span class="sxs-lookup"><span data-stu-id="c98e2-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="c98e2-731">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="c98e2-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
