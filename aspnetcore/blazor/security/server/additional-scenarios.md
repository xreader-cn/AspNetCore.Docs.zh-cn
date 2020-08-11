---
title: ASP.NET Core Blazor Server 其他安全方案
author: guardrex
description: 了解如何为其他安全方案配置 Blazor Server。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 8f112a4d71e44cae112e9854fc77dfda4af5a47a
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818906"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="196f7-103">ASP.NET Core Blazor Server 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="196f7-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="196f7-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="196f7-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="196f7-105">将令牌传递到 Blazor Server 应用</span><span class="sxs-lookup"><span data-stu-id="196f7-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="196f7-106">可以使用本节中介绍的方法将 Blazor Server 应用中 Razor 组件外部可用的令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="196f7-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="196f7-107">有关示例代码（包括完整的 `Startup.ConfigureServices` 示例），请参阅[将令牌传递到服务器端 Blazor 应用程序](https://github.com/javiercn/blazor-server-aad-sample)。</span><span class="sxs-lookup"><span data-stu-id="196f7-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="196f7-108">与对常规 Razor Pages 或 MVC 应用进行身份验证一样，对 Blazor Server 应用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="196f7-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="196f7-109">预配令牌并将其保存到身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="196f7-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="196f7-110">例如：</span><span class="sxs-lookup"><span data-stu-id="196f7-110">For example:</span></span>

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

<span data-ttu-id="196f7-111">定义一个类，以在初始应用状态下使用访问令牌和刷新令牌传递它：</span><span class="sxs-lookup"><span data-stu-id="196f7-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="196f7-112">定义可在 Blazor 应用中使用的作用域令牌提供程序服务，以解析[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 中的令牌：</span><span class="sxs-lookup"><span data-stu-id="196f7-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="196f7-113">在 `Startup.ConfigureServices` 中，为以下对象添加服务：</span><span class="sxs-lookup"><span data-stu-id="196f7-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="196f7-114">在 `_Host.cshtml` 文件中，创建 `InitialApplicationState` 实例，并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="196f7-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="196f7-115">在 `App` 组件 (`App.razor`) 中，解析服务并使用参数中的数据对其进行初始化：</span><span class="sxs-lookup"><span data-stu-id="196f7-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="196f7-116">在发出安全 API 请求的服务中，注入令牌提供程序并检索令牌以调用 API：</span><span class="sxs-lookup"><span data-stu-id="196f7-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="196f7-117">设置身份验证方案</span><span class="sxs-lookup"><span data-stu-id="196f7-117">Set the authentication scheme</span></span>

<span data-ttu-id="196f7-118">对于使用多个身份验证中间件并因此具有多个身份验证方案的应用，可以在 `Startup.Configure` 的终结点配置中显式设置 Blazor 使用的方案。</span><span class="sxs-lookup"><span data-stu-id="196f7-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="196f7-119">下面的示例设置 Azure Active Directory 方案：</span><span class="sxs-lookup"><span data-stu-id="196f7-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="196f7-120">使用 OpenID Connect (OIDC) v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="196f7-120">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="196f7-121">身份验证库和 Blazor 模板使用 OpenID Connect (OIDC) v1.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="196f7-121">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="196f7-122">要使用 v2.0 终结点，请在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 中配置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 选项：</span><span class="sxs-lookup"><span data-stu-id="196f7-122">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="196f7-123">也可以在应用设置 (`appsettings.json`) 文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="196f7-123">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="196f7-124">如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。</span><span class="sxs-lookup"><span data-stu-id="196f7-124">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="196f7-125">使用 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 键在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 或应用设置文件中设置属性。</span><span class="sxs-lookup"><span data-stu-id="196f7-125">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="196f7-126">代码更改</span><span class="sxs-lookup"><span data-stu-id="196f7-126">Code changes</span></span>

* <span data-ttu-id="196f7-127">ID 令牌中的声明列表针对 v2.0 终结点会发生更改。</span><span class="sxs-lookup"><span data-stu-id="196f7-127">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="196f7-128">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="196f7-128">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="196f7-129">（位于 Azure 文档中）。</span><span class="sxs-lookup"><span data-stu-id="196f7-129">in the Azure documentation.</span></span>
* <span data-ttu-id="196f7-130">由于在 v2.0 终结点的范围 URI 中指定了资源，请删除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 中的 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 属性设置：</span><span class="sxs-lookup"><span data-stu-id="196f7-130">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="196f7-131">可以在 OIDC 提供程序应用注册说明中找到要使用的应用 ID URI。</span><span class="sxs-lookup"><span data-stu-id="196f7-131">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
