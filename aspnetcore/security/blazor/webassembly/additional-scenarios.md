---
<span data-ttu-id="6d92d-101">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-103">'Blazor'</span></span>
- <span data-ttu-id="6d92d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-104">'Identity'</span></span>
- <span data-ttu-id="6d92d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-106">'Razor'</span></span>
- <span data-ttu-id="6d92d-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="6d92d-108">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="6d92d-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="6d92d-109">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="6d92d-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="6d92d-110">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="6d92d-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="6d92d-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>服务可用于 <xref:System.Net.Http.HttpClient> 将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="6d92d-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="6d92d-112">使用现有服务获取令牌 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="6d92d-113">如果无法获取令牌， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 则会引发。</span><span class="sxs-lookup"><span data-stu-id="6d92d-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="6d92d-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>提供了一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航到标识提供程序以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="6d92d-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>使用方法，可以配置授权 url、范围和返回 URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="6d92d-116">在下面的示例中， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> 在中配置 `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="6d92d-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6d92d-117">为方便起见， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 将包含的应用程序基址预配置为授权 URL。</span><span class="sxs-lookup"><span data-stu-id="6d92d-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="6d92d-118">启用身份验证的 Blazor WebAssembly 模板现在 <xref:System.Net.Http.IHttpClientFactory> 在服务器 API 项目中使用，以使用设置 <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="6d92d-119">在前面的示例中使用创建客户端的位置提供了在 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> 向服务器项目发出请求时提供访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="6d92d-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="6d92d-120">然后，将使用配置的 <xref:System.Net.Http.HttpClient> 来使用简单的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)模式发出授权请求。</span><span class="sxs-lookup"><span data-stu-id="6d92d-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="6d92d-121">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="6d92d-122">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="6d92d-122">Typed HttpClient</span></span>

<span data-ttu-id="6d92d-123">可以定义一个类型化客户端，用于处理单个类中的所有 HTTP 和标记获取问题。</span><span class="sxs-lookup"><span data-stu-id="6d92d-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="6d92d-124">WeatherForecastClient.cs  ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="6d92d-125">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="6d92d-126">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="6d92d-127">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="6d92d-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="6d92d-128">可以进一步 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d92d-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="6d92d-129">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="6d92d-130">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="6d92d-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="6d92d-131">如果 Blazor WebAssembly 应用程序通常使用安全默认值 <xref:System.Net.Http.HttpClient> ，则该应用程序还可以通过配置命名的来进行未经身份验证或未授权的 web API 请求 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="6d92d-132">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="6d92d-133">除了现有的安全默认注册以外，以上注册也是如此 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="6d92d-134">组件从创建， <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 以发出未经身份验证或未授权的请求：</span><span class="sxs-lookup"><span data-stu-id="6d92d-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="6d92d-135">对于前面的示例，服务器 API 中的控制器 `WeatherForecastNoAuthenticationController` 未标记为 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="6d92d-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="6d92d-136">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="6d92d-136">Request additional access tokens</span></span>

<span data-ttu-id="6d92d-137">可以通过调用手动获取访问令牌 `IAccessTokenProvider.RequestAccessToken` 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="6d92d-138">在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="6d92d-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="6d92d-139">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="6d92d-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="6d92d-140">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6d92d-141">`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用一组给定的范围设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="6d92d-142">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="6d92d-142">In a Razor component:</span></span>

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

<span data-ttu-id="6d92d-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>返回</span><span class="sxs-lookup"><span data-stu-id="6d92d-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="6d92d-144">`true``token`供使用的。</span><span class="sxs-lookup"><span data-stu-id="6d92d-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="6d92d-145">`false`如果未检索到标记，则为。</span><span class="sxs-lookup"><span data-stu-id="6d92d-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="6d92d-146">带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="6d92d-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="6d92d-147">在 WebAssembly 应用程序的 WebAssembly 上运行时 Blazor ， [HttpClient](xref:fundamentals/http-requests)和 <xref:System.Net.Http.HttpRequestMessage> 可用于自定义请求。</span><span class="sxs-lookup"><span data-stu-id="6d92d-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="6d92d-148">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="6d92d-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="6d92d-149">以下组件向 `POST` 服务器上的待办事项列表 API 终结点发出请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="6d92d-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="6d92d-150">.NET WebAssembly 的实现 <xref:System.Net.Http.HttpClient> 使用[WindowOrWorkerGlobalScope （）](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="6d92d-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="6d92d-151">提取允许配置几个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="6d92d-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="6d92d-152">HTTP fetch 请求选项可配置 <xref:System.Net.Http.HttpRequestMessage> 如下表中所示的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d92d-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="6d92d-153">扩展方法</span><span class="sxs-lookup"><span data-stu-id="6d92d-153">Extension method</span></span> | <span data-ttu-id="6d92d-154">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="6d92d-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="6d92d-155">凭据</span><span class="sxs-lookup"><span data-stu-id="6d92d-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="6d92d-156">区</span><span class="sxs-lookup"><span data-stu-id="6d92d-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="6d92d-157">mode</span><span class="sxs-lookup"><span data-stu-id="6d92d-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="6d92d-158">完整性</span><span class="sxs-lookup"><span data-stu-id="6d92d-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="6d92d-159">您可以使用更通用的扩展方法来设置其他选项 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="6d92d-160">HTTP 响应通常在 Blazor WebAssembly 应用中进行缓冲，以支持对响应内容进行同步读取。</span><span class="sxs-lookup"><span data-stu-id="6d92d-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="6d92d-161">若要启用对响应流的支持，请 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 对请求使用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d92d-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="6d92d-162">若要在跨域请求中包含凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="6d92d-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="6d92d-163">有关获取 API 选项的详细信息，请参阅[MDN web 文档： WindowOrWorkerGlobalScope （）:P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="6d92d-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="6d92d-164">在 CORS 请求上发送凭据（授权 cookie/标头）时， `Authorization` cors 策略必须允许标头。</span><span class="sxs-lookup"><span data-stu-id="6d92d-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="6d92d-165">以下策略包括的配置：</span><span class="sxs-lookup"><span data-stu-id="6d92d-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="6d92d-166">请求源（ `http://localhost:5000` 、 `https://localhost:5001` ）。</span><span class="sxs-lookup"><span data-stu-id="6d92d-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="6d92d-167">任何方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="6d92d-167">Any method (verb).</span></span>
* <span data-ttu-id="6d92d-168">`Content-Type`和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="6d92d-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="6d92d-169">若要允许自定义标头（例如 `x-custom-header` ），请在调用时列出标头 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="6d92d-170">由客户端 JavaScript 代码设置的凭据（ `credentials` 属性设置为 `include` ）。</span><span class="sxs-lookup"><span data-stu-id="6d92d-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="6d92d-171">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。</span><span class="sxs-lookup"><span data-stu-id="6d92d-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="6d92d-172">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="6d92d-172">Handle token request errors</span></span>

<span data-ttu-id="6d92d-173">当单页面应用程序（SPA）使用 Open ID Connect （OIDC）对用户进行身份验证时，身份验证状态将 Identity 以会话 cookie （设置为用户提供凭据的用户的结果）的形式保留在 SPA 中的本地和提供程序（IP）中。</span><span class="sxs-lookup"><span data-stu-id="6d92d-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="6d92d-174">通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="6d92d-175">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="6d92d-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="6d92d-176">在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。</span><span class="sxs-lookup"><span data-stu-id="6d92d-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="6d92d-177">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。</span><span class="sxs-lookup"><span data-stu-id="6d92d-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="6d92d-178">如果用户访问和注销，则会发生这种情况 `https://login.microsoftonline.com` 。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="6d92d-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="6d92d-179">此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="6d92d-180">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="6d92d-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="6d92d-181">它们是 Spa 的性质组成部分。</span><span class="sxs-lookup"><span data-stu-id="6d92d-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="6d92d-182">如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="6d92d-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="6d92d-183">当应用执行对受保护资源的 API 调用时，必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="6d92d-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="6d92d-184">若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="6d92d-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="6d92d-185">即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。</span><span class="sxs-lookup"><span data-stu-id="6d92d-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="6d92d-186">当应用请求令牌时，有两个可能的结果：</span><span class="sxs-lookup"><span data-stu-id="6d92d-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="6d92d-187">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="6d92d-188">请求失败，应用必须重新对用户进行身份验证才能获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="6d92d-189">如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="6d92d-190">增加复杂程度的方法有多种：</span><span class="sxs-lookup"><span data-stu-id="6d92d-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="6d92d-191">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="6d92d-191">Store the current page state in session storage.</span></span> <span data-ttu-id="6d92d-192">在[OnInitializedAsync 生命周期事件](xref:blazor/lifecycle#component-initialization-methods)（ <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ）期间，请检查状态是否可以恢复，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="6d92d-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="6d92d-193">添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="6d92d-194">添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="6d92d-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="6d92d-195">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="6d92d-195">The following example shows how to:</span></span>

* <span data-ttu-id="6d92d-196">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="6d92d-197">使用查询字符串参数恢复先前状态身份验证。</span><span class="sxs-lookup"><span data-stu-id="6d92d-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="6d92d-198">在身份验证操作之前保存应用程序状态</span><span class="sxs-lookup"><span data-stu-id="6d92d-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="6d92d-199">在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="6d92d-200">当使用类似于状态容器的内容时，如果你想要在身份验证成功后还原状态，则可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="6d92d-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="6d92d-201">可以使用自定义身份验证状态对象来保持应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="6d92d-202">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="6d92d-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="6d92d-203">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="6d92d-203">Customize app routes</span></span>

<span data-ttu-id="6d92d-204">默认情况下， [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="6d92d-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="6d92d-205">路由</span><span class="sxs-lookup"><span data-stu-id="6d92d-205">Route</span></span>                            | <span data-ttu-id="6d92d-206">目的</span><span class="sxs-lookup"><span data-stu-id="6d92d-206">Purpose</span></span> |
| ---
<span data-ttu-id="6d92d-207">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-209">'Blazor'</span></span>
- <span data-ttu-id="6d92d-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-210">'Identity'</span></span>
- <span data-ttu-id="6d92d-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-212">'Razor'</span></span>
- <span data-ttu-id="6d92d-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-214">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-216">'Blazor'</span></span>
- <span data-ttu-id="6d92d-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-217">'Identity'</span></span>
- <span data-ttu-id="6d92d-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-219">'Razor'</span></span>
- <span data-ttu-id="6d92d-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-221">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-223">'Blazor'</span></span>
- <span data-ttu-id="6d92d-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-224">'Identity'</span></span>
- <span data-ttu-id="6d92d-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-226">'Razor'</span></span>
- <span data-ttu-id="6d92d-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-228">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-230">'Blazor'</span></span>
- <span data-ttu-id="6d92d-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-231">'Identity'</span></span>
- <span data-ttu-id="6d92d-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-233">'Razor'</span></span>
- <span data-ttu-id="6d92d-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-235">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-237">'Blazor'</span></span>
- <span data-ttu-id="6d92d-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-238">'Identity'</span></span>
- <span data-ttu-id="6d92d-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-240">'Razor'</span></span>
- <span data-ttu-id="6d92d-241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-242">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-244">'Blazor'</span></span>
- <span data-ttu-id="6d92d-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-245">'Identity'</span></span>
- <span data-ttu-id="6d92d-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-247">'Razor'</span></span>
- <span data-ttu-id="6d92d-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-249">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-251">'Blazor'</span></span>
- <span data-ttu-id="6d92d-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-252">'Identity'</span></span>
- <span data-ttu-id="6d92d-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-254">'Razor'</span></span>
- <span data-ttu-id="6d92d-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-256">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-258">'Blazor'</span></span>
- <span data-ttu-id="6d92d-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-259">'Identity'</span></span>
- <span data-ttu-id="6d92d-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-261">'Razor'</span></span>
- <span data-ttu-id="6d92d-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-263">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-265">'Blazor'</span></span>
- <span data-ttu-id="6d92d-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-266">'Identity'</span></span>
- <span data-ttu-id="6d92d-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-268">'Razor'</span></span>
- <span data-ttu-id="6d92d-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-270">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-272">'Blazor'</span></span>
- <span data-ttu-id="6d92d-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-273">'Identity'</span></span>
- <span data-ttu-id="6d92d-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-275">'Razor'</span></span>
- <span data-ttu-id="6d92d-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-277">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-279">'Blazor'</span></span>
- <span data-ttu-id="6d92d-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-280">'Identity'</span></span>
- <span data-ttu-id="6d92d-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-282">'Razor'</span></span>
- <span data-ttu-id="6d92d-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-284">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-286">'Blazor'</span></span>
- <span data-ttu-id="6d92d-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-287">'Identity'</span></span>
- <span data-ttu-id="6d92d-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-289">'Razor'</span></span>
- <span data-ttu-id="6d92d-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-291">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-293">'Blazor'</span></span>
- <span data-ttu-id="6d92d-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-294">'Identity'</span></span>
- <span data-ttu-id="6d92d-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-296">'Razor'</span></span>
- <span data-ttu-id="6d92d-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-298">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-300">'Blazor'</span></span>
- <span data-ttu-id="6d92d-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-301">'Identity'</span></span>
- <span data-ttu-id="6d92d-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-303">'Razor'</span></span>
- <span data-ttu-id="6d92d-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-304">'SignalR' uid:</span></span> 

<span data-ttu-id="6d92d-305">---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-307">'Blazor'</span></span>
- <span data-ttu-id="6d92d-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-308">'Identity'</span></span>
- <span data-ttu-id="6d92d-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-310">'Razor'</span></span>
- <span data-ttu-id="6d92d-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-311">'SignalR' uid:</span></span> 

<span data-ttu-id="6d92d-312">---- | |`authentication/login`           |触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="6d92d-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="6d92d-313">| |`authentication/login-callback`  |处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="6d92d-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="6d92d-314">| |`authentication/login-failed`    |当登录操作因某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="6d92d-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="6d92d-315">| |`authentication/logout`          |触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="6d92d-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="6d92d-316">| |`authentication/logout-callback` |处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="6d92d-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="6d92d-317">| |`authentication/logout-failed`   |出于某些原因，在注销操作失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="6d92d-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="6d92d-318">| |`authentication/logged-out`      |指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="6d92d-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="6d92d-319">| |`authentication/profile`         |触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="6d92d-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="6d92d-320">| |`authentication/register`        |触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="6d92d-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="6d92d-321">可以通过配置上表中显示的路由 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6d92d-322">设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="6d92d-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="6d92d-323">在下面的示例中，所有路径都以为前缀 `/security` 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="6d92d-324">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="6d92d-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="6d92d-325">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6d92d-326">如果要求调用完全不同的路径，请按前面所述设置路由，并 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 使用显式操作参数呈现：</span><span class="sxs-lookup"><span data-stu-id="6d92d-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="6d92d-327">如果你选择这样做，则允许你将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="6d92d-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="6d92d-328">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="6d92d-328">Customize the authentication user interface</span></span>

<span data-ttu-id="6d92d-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>为每个身份验证状态包含一组默认的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="6d92d-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="6d92d-330">可以通过传入自定义来自定义每个状态 <xref:Microsoft.AspNetCore.Components.RenderFragment> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="6d92d-331">若要在初始登录过程中自定义显示的文本，可以更改，如下所示 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="6d92d-332">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="6d92d-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="6d92d-333"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>有一个可用于下表中所示的每个身份验证路由的片段。</span><span class="sxs-lookup"><span data-stu-id="6d92d-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="6d92d-334">路由</span><span class="sxs-lookup"><span data-stu-id="6d92d-334">Route</span></span>                            | <span data-ttu-id="6d92d-335">片段</span><span class="sxs-lookup"><span data-stu-id="6d92d-335">Fragment</span></span>                |
| ---
<span data-ttu-id="6d92d-336">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-338">'Blazor'</span></span>
- <span data-ttu-id="6d92d-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-339">'Identity'</span></span>
- <span data-ttu-id="6d92d-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-341">'Razor'</span></span>
- <span data-ttu-id="6d92d-342">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-343">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-345">'Blazor'</span></span>
- <span data-ttu-id="6d92d-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-346">'Identity'</span></span>
- <span data-ttu-id="6d92d-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-348">'Razor'</span></span>
- <span data-ttu-id="6d92d-349">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-350">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-352">'Blazor'</span></span>
- <span data-ttu-id="6d92d-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-353">'Identity'</span></span>
- <span data-ttu-id="6d92d-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-355">'Razor'</span></span>
- <span data-ttu-id="6d92d-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-357">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-359">'Blazor'</span></span>
- <span data-ttu-id="6d92d-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-360">'Identity'</span></span>
- <span data-ttu-id="6d92d-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-362">'Razor'</span></span>
- <span data-ttu-id="6d92d-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-364">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-366">'Blazor'</span></span>
- <span data-ttu-id="6d92d-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-367">'Identity'</span></span>
- <span data-ttu-id="6d92d-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-369">'Razor'</span></span>
- <span data-ttu-id="6d92d-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-371">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-373">'Blazor'</span></span>
- <span data-ttu-id="6d92d-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-374">'Identity'</span></span>
- <span data-ttu-id="6d92d-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-376">'Razor'</span></span>
- <span data-ttu-id="6d92d-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-378">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-380">'Blazor'</span></span>
- <span data-ttu-id="6d92d-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-381">'Identity'</span></span>
- <span data-ttu-id="6d92d-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-383">'Razor'</span></span>
- <span data-ttu-id="6d92d-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-385">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-387">'Blazor'</span></span>
- <span data-ttu-id="6d92d-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-388">'Identity'</span></span>
- <span data-ttu-id="6d92d-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-390">'Razor'</span></span>
- <span data-ttu-id="6d92d-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-392">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-394">'Blazor'</span></span>
- <span data-ttu-id="6d92d-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-395">'Identity'</span></span>
- <span data-ttu-id="6d92d-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-397">'Razor'</span></span>
- <span data-ttu-id="6d92d-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-399">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-401">'Blazor'</span></span>
- <span data-ttu-id="6d92d-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-402">'Identity'</span></span>
- <span data-ttu-id="6d92d-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-404">'Razor'</span></span>
- <span data-ttu-id="6d92d-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-406">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-408">'Blazor'</span></span>
- <span data-ttu-id="6d92d-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-409">'Identity'</span></span>
- <span data-ttu-id="6d92d-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-411">'Razor'</span></span>
- <span data-ttu-id="6d92d-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-413">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-415">'Blazor'</span></span>
- <span data-ttu-id="6d92d-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-416">'Identity'</span></span>
- <span data-ttu-id="6d92d-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-418">'Razor'</span></span>
- <span data-ttu-id="6d92d-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-420">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-422">'Blazor'</span></span>
- <span data-ttu-id="6d92d-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-423">'Identity'</span></span>
- <span data-ttu-id="6d92d-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-425">'Razor'</span></span>
- <span data-ttu-id="6d92d-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-427">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-429">'Blazor'</span></span>
- <span data-ttu-id="6d92d-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-430">'Identity'</span></span>
- <span data-ttu-id="6d92d-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-432">'Razor'</span></span>
- <span data-ttu-id="6d92d-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-433">'SignalR' uid:</span></span> 

<span data-ttu-id="6d92d-434">---------------- |---标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" 作者：说明： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-436">'Blazor'</span></span>
- <span data-ttu-id="6d92d-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-437">'Identity'</span></span>
- <span data-ttu-id="6d92d-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-439">'Razor'</span></span>
- <span data-ttu-id="6d92d-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-441">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-443">'Blazor'</span></span>
- <span data-ttu-id="6d92d-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-444">'Identity'</span></span>
- <span data-ttu-id="6d92d-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-446">'Razor'</span></span>
- <span data-ttu-id="6d92d-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-448">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-450">'Blazor'</span></span>
- <span data-ttu-id="6d92d-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-451">'Identity'</span></span>
- <span data-ttu-id="6d92d-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-453">'Razor'</span></span>
- <span data-ttu-id="6d92d-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-455">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-457">'Blazor'</span></span>
- <span data-ttu-id="6d92d-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-458">'Identity'</span></span>
- <span data-ttu-id="6d92d-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-460">'Razor'</span></span>
- <span data-ttu-id="6d92d-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-462">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-464">'Blazor'</span></span>
- <span data-ttu-id="6d92d-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-465">'Identity'</span></span>
- <span data-ttu-id="6d92d-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-467">'Razor'</span></span>
- <span data-ttu-id="6d92d-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-469">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-471">'Blazor'</span></span>
- <span data-ttu-id="6d92d-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-472">'Identity'</span></span>
- <span data-ttu-id="6d92d-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-474">'Razor'</span></span>
- <span data-ttu-id="6d92d-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-476">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-478">'Blazor'</span></span>
- <span data-ttu-id="6d92d-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-479">'Identity'</span></span>
- <span data-ttu-id="6d92d-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-481">'Razor'</span></span>
- <span data-ttu-id="6d92d-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-483">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-485">'Blazor'</span></span>
- <span data-ttu-id="6d92d-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-486">'Identity'</span></span>
- <span data-ttu-id="6d92d-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-488">'Razor'</span></span>
- <span data-ttu-id="6d92d-489">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d92d-490">标题： "ASP.NET Core Blazor WebAssembly 其他安全方案" author： description： "了解如何 Blazor 为其他安全方案配置 WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="6d92d-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6d92d-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d92d-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d92d-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-492">'Blazor'</span></span>
- <span data-ttu-id="6d92d-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d92d-493">'Identity'</span></span>
- <span data-ttu-id="6d92d-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d92d-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d92d-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d92d-495">'Razor'</span></span>
- <span data-ttu-id="6d92d-496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d92d-496">'SignalR' uid:</span></span> 

<span data-ttu-id="6d92d-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="6d92d-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="6d92d-498">自定义用户</span><span class="sxs-lookup"><span data-stu-id="6d92d-498">Customize the user</span></span>

<span data-ttu-id="6d92d-499">绑定到应用的用户可以自定义。</span><span class="sxs-lookup"><span data-stu-id="6d92d-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="6d92d-500">在下面的示例中，所有经过身份验证的用户都 `amr` 将收到每个用户身份验证方法的声明。</span><span class="sxs-lookup"><span data-stu-id="6d92d-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="6d92d-501">创建扩展类的类 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="6d92d-502">创建一个扩展的工厂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="6d92d-503">注册 `CustomAccountFactory` 要使用的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="6d92d-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="6d92d-504">以下任何注册都是有效的：</span><span class="sxs-lookup"><span data-stu-id="6d92d-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="6d92d-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="6d92d-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="6d92d-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="6d92d-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="6d92d-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="6d92d-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="6d92d-508">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="6d92d-508">Support prerendering with authentication</span></span>

<span data-ttu-id="6d92d-509">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="6d92d-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="6d92d-510">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="6d92d-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="6d92d-511">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="6d92d-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="6d92d-512">在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="6d92d-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="6d92d-513">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="6d92d-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="6d92d-514">在服务器应用程序的 `Startup.Configure` 方法中，将[终结点替换为。带有终结点的 MapFallbackToFile （"index .html"）](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [。MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：</span><span class="sxs-lookup"><span data-stu-id="6d92d-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="6d92d-515">在服务器应用中，如果不存在 Pages  文件夹，则创建它。</span><span class="sxs-lookup"><span data-stu-id="6d92d-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="6d92d-516">在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。  </span><span class="sxs-lookup"><span data-stu-id="6d92d-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="6d92d-517">将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。  </span><span class="sxs-lookup"><span data-stu-id="6d92d-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="6d92d-518">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="6d92d-518">Update the file's contents:</span></span>

* <span data-ttu-id="6d92d-519">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="6d92d-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="6d92d-520">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="6d92d-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="6d92d-521">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="6d92d-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="6d92d-522">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="6d92d-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="6d92d-523">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="6d92d-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="6d92d-524">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="6d92d-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="6d92d-525">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="6d92d-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="6d92d-526">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="6d92d-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="6d92d-527">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="6d92d-527">In this scenario:</span></span>

* <span data-ttu-id="6d92d-528">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="6d92d-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="6d92d-529">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="6d92d-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="6d92d-530">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="6d92d-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="6d92d-531">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="6d92d-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="6d92d-532">Identity使用第三方登录提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d92d-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="6d92d-533">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="6d92d-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="6d92d-534">当用户登录时，会在 Identity 身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="6d92d-535">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="6d92d-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="6d92d-536">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="6d92d-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="6d92d-537">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="6d92d-538">在该处，使用第三方访问令牌直接从客户端调用第三方 API 资源 Identity 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="6d92d-539">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="6d92d-539">We don't recommend this approach.</span></span> <span data-ttu-id="6d92d-540">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="6d92d-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="6d92d-541">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="6d92d-542">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="6d92d-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="6d92d-543">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="6d92d-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="6d92d-544">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="6d92d-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="6d92d-545">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="6d92d-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="6d92d-546">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="6d92d-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="6d92d-547">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="6d92d-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="6d92d-548">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="6d92d-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="6d92d-549">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="6d92d-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="6d92d-550">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="6d92d-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="6d92d-551">使用 Open ID Connect （OIDC） v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="6d92d-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="6d92d-552">身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="6d92d-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="6d92d-553">若要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="6d92d-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="6d92d-554">在下面的示例中，通过将一个段附加到属性，为 v2.0 配置了 AAD `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ：</span><span class="sxs-lookup"><span data-stu-id="6d92d-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="6d92d-555">或者，可以在应用设置（*appsettings*）文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="6d92d-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="6d92d-556">如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接设置属性。</span><span class="sxs-lookup"><span data-stu-id="6d92d-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="6d92d-557">在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 应用程序设置文件（*appsettings*）中或用键设置属性 `Authority` 。</span><span class="sxs-lookup"><span data-stu-id="6d92d-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="6d92d-558">对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。</span><span class="sxs-lookup"><span data-stu-id="6d92d-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="6d92d-559">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="6d92d-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
