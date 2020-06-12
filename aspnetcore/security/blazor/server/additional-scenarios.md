---
title: ASP.NET Core Blazor Server 其他安全方案
author: guardrex
description: 了解如何 Blazor 为其他安全方案配置服务器。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 159d418a78caa3954294ad0a1067654d895147f7
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679665"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server 其他安全方案

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>将令牌传递给 Blazor 服务器应用

Razor Blazor 可以使用本节中所述的方法，将服务器应用程序中组件以外可用的令牌传递给组件。 有关示例代码，包括完整的 `Startup.ConfigureServices` 示例，请参阅将[令牌传递给服务器端 Blazor 应用程序](https://github.com/javiercn/blazor-server-aad-sample)。

对服务器应用进行身份验证，就 Blazor 像使用常规 Razor 页面或 MVC 应用一样。 预配令牌并将其保存到身份验证 cookie。 例如：

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

定义可在应用内使用的**作用域**令牌提供程序服务， Blazor 以从[依赖关系注入（DI）](xref:blazor/dependency-injection)解析令牌：

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在中 `Startup.ConfigureServices` ，为以下项添加服务：

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

在 *_Host cshtml*文件中，创建和实例， `InitialApplicationState` 并将其作为参数传递给应用：

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

在 `App` 组件（*app.config*）中，解析服务，并用参数中的数据对其进行初始化：

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

## <a name="set-the-authentication-scheme"></a>设置身份验证方案

对于使用多个身份验证中间件的应用程序，因此具有多个身份验证方案， Blazor 可以在的终结点配置中显式设置使用方案 `Startup.Configure` 。 下面的示例设置 Azure Active Directory 方案：

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>使用 Open ID Connect （OIDC） v2.0 终结点

身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。 若要使用 v2.0 终结点，请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 在中配置选项 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

或者，可以在应用设置（*appsettings.js*）文件中进行设置：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接设置属性。 在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 应用设置文件中或在应用设置文件中用键设置属性 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 。

### <a name="code-changes"></a>代码更改

* 对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。 有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) 在 Azure 文档中。
* 由于在 v2.0 终结点的范围 Uri 中指定了资源，请删除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 中的属性设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：

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

可以在 OIDC 提供程序应用注册说明中找到要使用的应用 ID URI。
