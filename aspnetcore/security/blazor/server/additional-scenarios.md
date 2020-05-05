---
title: ASP.NET Core Blazor Server 其他安全方案
author: guardrex
description: 了解如何为其他Blazor安全方案配置服务器。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772799"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="538d5-103">ASP.NET Core Blazor Server 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="538d5-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="538d5-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="538d5-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="538d5-105">将令牌传递给Blazor服务器应用</span><span class="sxs-lookup"><span data-stu-id="538d5-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="538d5-106">可以使用本节中所Razor述的方法Blazor ，将服务器应用程序中组件以外可用的令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="538d5-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="538d5-107">有关示例代码，包括完整`Startup.ConfigureServices`的示例，请参阅将[令牌传递给服务器端Blazor应用程序](https://github.com/javiercn/blazor-server-aad-sample)。</span><span class="sxs-lookup"><span data-stu-id="538d5-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="538d5-108">对Blazor服务器应用进行身份验证，就像使用Razor常规页面或 MVC 应用一样。</span><span class="sxs-lookup"><span data-stu-id="538d5-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="538d5-109">预配令牌并将其保存到身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="538d5-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="538d5-110">例如：</span><span class="sxs-lookup"><span data-stu-id="538d5-110">For example:</span></span>

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

<span data-ttu-id="538d5-111">定义一个类，使其在初始应用状态中包含访问和刷新令牌：</span><span class="sxs-lookup"><span data-stu-id="538d5-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="538d5-112">定义可在Blazor应用内使用的**作用域**令牌提供程序服务，以从[依赖关系注入（DI）](xref:blazor/dependency-injection)解析令牌：</span><span class="sxs-lookup"><span data-stu-id="538d5-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="538d5-113">在`Startup.ConfigureServices`中，为以下项添加服务：</span><span class="sxs-lookup"><span data-stu-id="538d5-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="538d5-114">在 *_Host cshtml*文件中，创建和实例， `InitialApplicationState`并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="538d5-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="538d5-115">在`App`组件（*app.config*）中，解析服务，并用参数中的数据对其进行初始化：</span><span class="sxs-lookup"><span data-stu-id="538d5-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="538d5-116">在进行安全 API 请求的服务中，插入令牌提供程序并检索用于调用 API 的令牌：</span><span class="sxs-lookup"><span data-stu-id="538d5-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
