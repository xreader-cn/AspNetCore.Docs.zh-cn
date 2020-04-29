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
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206346"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server 其他安全方案

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>将令牌传递给Blazor服务器应用

可以使用本节中所述的方法， Blazor将服务器应用程序中的 Razor 组件以外可用的令牌传递给组件。 有关示例代码，包括完整`Startup.ConfigureServices`的示例，请参阅将[令牌传递给服务器端Blazor应用程序](https://github.com/javiercn/blazor-server-aad-sample)。

对Blazor服务器应用程序进行身份验证，就像使用常规 RAZOR PAGES 或 MVC 应用程序一样。 预配令牌并将其保存到身份验证 cookie。 例如：

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

定义一个类，使其在初始应用状态中包含访问和刷新令牌：

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

定义可在Blazor应用内使用的**作用域**令牌提供程序服务，以从[依赖关系注入（DI）](xref:blazor/dependency-injection)解析令牌：

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在`Startup.ConfigureServices`中，为以下项添加服务：

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

在 *_Host cshtml*文件中，创建和实例， `InitialApplicationState`并将其作为参数传递给应用：

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

在`App`组件（*app.config*）中，解析服务，并用参数中的数据对其进行初始化：

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

在进行安全 API 请求的服务中，插入令牌提供程序并检索用于调用 API 的令牌：

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
