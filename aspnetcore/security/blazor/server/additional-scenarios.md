---
<span data-ttu-id="80050-101">标题： "ASP.NET Core Blazor 服务器其他安全方案作者：说明：" 了解如何 Blazor 为其他安全方案配置服务器。 "</span><span class="sxs-lookup"><span data-stu-id="80050-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="80050-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80050-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80050-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80050-103">'Blazor'</span></span>
- <span data-ttu-id="80050-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80050-104">'Identity'</span></span>
- <span data-ttu-id="80050-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80050-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="80050-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80050-106">'Razor'</span></span>
- <span data-ttu-id="80050-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80050-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="80050-108">ASP.NET Core Blazor Server 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="80050-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="80050-109">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="80050-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="80050-110">将令牌传递给 Blazor 服务器应用</span><span class="sxs-lookup"><span data-stu-id="80050-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="80050-111">Razor Blazor 可以使用本节中所述的方法，将服务器应用程序中组件以外可用的令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="80050-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="80050-112">有关示例代码，包括完整的 `Startup.ConfigureServices` 示例，请参阅将[令牌传递给服务器端 Blazor 应用程序](https://github.com/javiercn/blazor-server-aad-sample)。</span><span class="sxs-lookup"><span data-stu-id="80050-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="80050-113">对服务器应用进行身份验证，就 Blazor 像使用常规 Razor 页面或 MVC 应用一样。</span><span class="sxs-lookup"><span data-stu-id="80050-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="80050-114">预配令牌并将其保存到身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="80050-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="80050-115">例如：</span><span class="sxs-lookup"><span data-stu-id="80050-115">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="80050-116">定义一个类，使其在初始应用状态中包含访问和刷新令牌：</span><span class="sxs-lookup"><span data-stu-id="80050-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="80050-117">定义可在应用内使用的**作用域**令牌提供程序服务， Blazor 以从[依赖关系注入（DI）](xref:blazor/dependency-injection)解析令牌：</span><span class="sxs-lookup"><span data-stu-id="80050-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="80050-118">在中 `Startup.ConfigureServices` ，为以下项添加服务：</span><span class="sxs-lookup"><span data-stu-id="80050-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="80050-119">在 *_Host cshtml*文件中，创建和实例， `InitialApplicationState` 并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="80050-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="80050-120">在 `App` 组件（*app.config*）中，解析服务，并用参数中的数据对其进行初始化：</span><span class="sxs-lookup"><span data-stu-id="80050-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="80050-121">在进行安全 API 请求的服务中，插入令牌提供程序并检索用于调用 API 的令牌：</span><span class="sxs-lookup"><span data-stu-id="80050-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="80050-122">使用 Open ID Connect （OIDC） v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="80050-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="80050-123">身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="80050-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="80050-124">若要使用 v2.0 终结点，请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 在中配置选项 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：</span><span class="sxs-lookup"><span data-stu-id="80050-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="80050-125">或者，可以在应用设置（*appsettings*）文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="80050-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="80050-126">如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接设置属性。</span><span class="sxs-lookup"><span data-stu-id="80050-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="80050-127">在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 应用设置文件中或在应用设置文件中用键设置属性 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 。</span><span class="sxs-lookup"><span data-stu-id="80050-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="80050-128">代码更改</span><span class="sxs-lookup"><span data-stu-id="80050-128">Code changes</span></span>

* <span data-ttu-id="80050-129">对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。</span><span class="sxs-lookup"><span data-stu-id="80050-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="80050-130">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="80050-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="80050-131">在 Azure 文档中。</span><span class="sxs-lookup"><span data-stu-id="80050-131">in the Azure documentation.</span></span>
* <span data-ttu-id="80050-132">由于在 v2.0 终结点的范围 Uri 中指定了资源，请删除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 中的属性设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：</span><span class="sxs-lookup"><span data-stu-id="80050-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="80050-133">可以在 OIDC 提供程序应用注册说明中找到要使用的应用 ID URI。</span><span class="sxs-lookup"><span data-stu-id="80050-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
