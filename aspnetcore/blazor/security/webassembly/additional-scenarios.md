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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="b85df-103">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="b85df-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="b85df-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b85df-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="b85df-105">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="b85df-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="b85df-106">可以将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 服务与 <xref:System.Net.Http.HttpClient> 一起使用，以将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="b85df-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="b85df-107">令牌是使用现有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 服务获取的。</span><span class="sxs-lookup"><span data-stu-id="b85df-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="b85df-108">如果无法获取令牌，则会引发 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>。</span><span class="sxs-lookup"><span data-stu-id="b85df-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="b85df-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航至标识提供者以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="b85df-110">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 方法将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 配置为授权的 URL、作用域和返回 URL。</span><span class="sxs-lookup"><span data-stu-id="b85df-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="b85df-111">使用以下两种方法之一为传出请求配置消息处理程序：</span><span class="sxs-lookup"><span data-stu-id="b85df-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="b85df-112">[自定义 AuthorizationMessageHandler 类](#custom-authorizationmessagehandler-class)（推荐使用\*\*）</span><span class="sxs-lookup"><span data-stu-id="b85df-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="b85df-113">配置 AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b85df-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="b85df-114">自定义 AuthorizationMessageHandler 类</span><span class="sxs-lookup"><span data-stu-id="b85df-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="b85df-115">在下面的示例中，自定义类扩展了可用于配置 <xref:System.Net.Http.HttpClient> 的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>：</span><span class="sxs-lookup"><span data-stu-id="b85df-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="b85df-116">在 `Program.Main`（Program.cs\*\*）中，使用自定义授权消息处理程序配置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="b85df-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="b85df-117">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求。</span><span class="sxs-lookup"><span data-stu-id="b85df-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="b85df-118">如果使用 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包）创建客户端的情况，则在向服务器 API 发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例：</span><span class="sxs-lookup"><span data-stu-id="b85df-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="b85df-119">配置 AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b85df-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="b85df-120">在下面的示例中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 在 `Program.Main` (Program.cs\*\*) 中配置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="b85df-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b85df-121">为了方便起见，包含 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>，它预先配置了应用基址作为授权的 URL。</span><span class="sxs-lookup"><span data-stu-id="b85df-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="b85df-122">支持身份验证的 Blazor WebAssembly 模板现在使用服务器 API 项目中的 <xref:System.Net.Http.IHttpClientFactory>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包），通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 设置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="b85df-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="b85df-123">如果使用上一示例中的 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> 创建客户端，则在向服务器项目发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="b85df-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b85df-124">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求：</span><span class="sxs-lookup"><span data-stu-id="b85df-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="b85df-125">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="b85df-125">Typed HttpClient</span></span>

<span data-ttu-id="b85df-126">可以定义类型化的客户端，以使用它处理单个类中的所有 HTTP 和令牌获取问题。</span><span class="sxs-lookup"><span data-stu-id="b85df-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="b85df-127">WeatherForecastClient.cs\*\*：</span><span class="sxs-lookup"><span data-stu-id="b85df-127">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="b85df-128">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `using static BlazorSample.Data;`）。</span><span class="sxs-lookup"><span data-stu-id="b85df-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="b85df-129">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="b85df-130">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-130">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="b85df-131">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="b85df-131">Configure the HttpClient handler</span></span>

<span data-ttu-id="b85df-132">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求进一步配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="b85df-132">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="b85df-133">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="b85df-134">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="b85df-134">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="b85df-135">如果 Blazor WebAssembly 应用通常使用安全的默认 <xref:System.Net.Http.HttpClient>，则该应用还可以通过配置命名的 <xref:System.Net.Http.HttpClient> 来发出未经身份验证或未经授权的 Web API 请求：</span><span class="sxs-lookup"><span data-stu-id="b85df-135">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="b85df-136">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-136">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b85df-137">先前的注册是对现有安全默认 <xref:System.Net.Http.HttpClient> 注册的补充。</span><span class="sxs-lookup"><span data-stu-id="b85df-137">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="b85df-138">组件从 <xref:System.Net.Http.IHttpClientFactory>（[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包）创建 <xref:System.Net.Http.HttpClient>，以发出未经身份验证或未经授权的请求：</span><span class="sxs-lookup"><span data-stu-id="b85df-138">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="b85df-139">服务器 API 中的控制器（上述示例中为 `WeatherForecastNoAuthenticationController`）未标记 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="b85df-139">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="b85df-140">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="b85df-140">Request additional access tokens</span></span>

<span data-ttu-id="b85df-141">可以通过调用 `IAccessTokenProvider.RequestAccessToken` 手动获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-141">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="b85df-142">在以下示例中，应用需要其他 Azure Active Directory (AAD) Microsoft Graph API 作用域才能读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="b85df-142">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="b85df-143">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，将在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="b85df-143">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="b85df-144">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-144">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b85df-145">`IAccessTokenProvider.RequestToken` 方法提供重载，该重载允许应用使用一组给定的作用域来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-145">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="b85df-146">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="b85df-146">In a Razor component:</span></span>

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

<span data-ttu-id="b85df-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：</span><span class="sxs-lookup"><span data-stu-id="b85df-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="b85df-148">如果使用 `token`，则为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b85df-148">`true` with the `token` for use.</span></span>
* <span data-ttu-id="b85df-149">如果未检索到令牌，则为 `false`。</span><span class="sxs-lookup"><span data-stu-id="b85df-149">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b85df-150">具有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="b85df-150">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="b85df-151">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，可以使用 [HttpClient](xref:fundamentals/http-requests) 和 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。</span><span class="sxs-lookup"><span data-stu-id="b85df-151">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="b85df-152">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="b85df-152">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="b85df-153">以下组件向服务器上的待办事项列表 API 终结点发出 `POST` 请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="b85df-153">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="b85df-154"><xref:System.Net.Http.HttpClient> 的 .NET WebAssembly 实现使用 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="b85df-154">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="b85df-155">提取允许配置多个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="b85df-155">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="b85df-156">使用下表中所示的 <xref:System.Net.Http.HttpRequestMessage> 扩展方法可配置 HTTP fetch 请求选项。</span><span class="sxs-lookup"><span data-stu-id="b85df-156">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="b85df-157">扩展方法</span><span class="sxs-lookup"><span data-stu-id="b85df-157">Extension method</span></span> | <span data-ttu-id="b85df-158">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="b85df-158">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="b85df-159">凭据</span><span class="sxs-lookup"><span data-stu-id="b85df-159">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="b85df-160">缓存</span><span class="sxs-lookup"><span data-stu-id="b85df-160">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="b85df-161">模式</span><span class="sxs-lookup"><span data-stu-id="b85df-161">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="b85df-162">完整性</span><span class="sxs-lookup"><span data-stu-id="b85df-162">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="b85df-163">可使用更通用的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 扩展方法设置其他选项。</span><span class="sxs-lookup"><span data-stu-id="b85df-163">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="b85df-164">HTTP 响应通常在 Blazor WebAssembly 应用中缓冲，以支持同步读取响应内容。</span><span class="sxs-lookup"><span data-stu-id="b85df-164">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="b85df-165">要支持响应流式处理，请对请求使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b85df-165">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="b85df-166">要在跨源请求中加入凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="b85df-166">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="b85df-167">有关 Fetch API 选项的详细信息，请参阅 [MDN Web 文档：WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="b85df-167">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="b85df-168">在 CORS 请求中发送凭据（授权 cookie/标头）时，CORS 策略必须允许使用 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="b85df-168">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="b85df-169">以下策略包括的配置用于：</span><span class="sxs-lookup"><span data-stu-id="b85df-169">The following policy includes configuration for:</span></span>

* <span data-ttu-id="b85df-170">请求来源（`http://localhost:5000`、`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="b85df-170">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="b85df-171">Any 方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="b85df-171">Any method (verb).</span></span>
* <span data-ttu-id="b85df-172">`Content-Type` 和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="b85df-172">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="b85df-173">若要允许使用自定义标头（例如 `x-custom-header`），请在调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 时列出标头。</span><span class="sxs-lookup"><span data-stu-id="b85df-173">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="b85df-174">由客户端 JavaScript 代码设置的凭据（`credentials` 属性设置为 `include`）。</span><span class="sxs-lookup"><span data-stu-id="b85df-174">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="b85df-175">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件 (Components/HTTPRequestTester.razor\*\*)。</span><span class="sxs-lookup"><span data-stu-id="b85df-175">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="b85df-176">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="b85df-176">Handle token request errors</span></span>

<span data-ttu-id="b85df-177">当单页应用程序 (SPA) 使用 Open ID Connect (OIDC) 对用户进行身份验证时，身份验证状态将以会话 cookie（因用户提供其凭据而设置）的形式在 SPA 内和 Identity 提供者 (IP) 中在本地进行维护。</span><span class="sxs-lookup"><span data-stu-id="b85df-177">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="b85df-178">IP 为用户发出的令牌通常在短时间（约 1 小时）内有效，因此客户端应用必须定期提取新令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-178">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="b85df-179">否则，在授予的令牌到期后，将注销用户。</span><span class="sxs-lookup"><span data-stu-id="b85df-179">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="b85df-180">在大多数情况下，由于身份验证状态或“会话”保留在 IP 中，因此 OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85df-180">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="b85df-181">在某些情况下，如果没有用户交互，客户端就无法获得令牌（例如，当用户出于某种原因而明确从 IP 注销时）。</span><span class="sxs-lookup"><span data-stu-id="b85df-181">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="b85df-182">如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些场景下，应用不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="b85df-182">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="b85df-183">另外，当前令牌过期后，如果没有用户交互，客户端将无法预配新令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-183">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="b85df-184">这些场景并不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85df-184">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="b85df-185">它们本就是 SPA 的一部分。</span><span class="sxs-lookup"><span data-stu-id="b85df-185">They are part of the nature of SPAs.</span></span> <span data-ttu-id="b85df-186">如果删除了身份验证 cookie，则使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="b85df-186">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="b85df-187">当应用对受保护的资源执行 API 调用时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="b85df-187">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="b85df-188">要预配新的访问令牌来调用 API，可能需要用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85df-188">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="b85df-189">即使客户端拥有看似有效的令牌，对服务器的调用也可能会失败，因为该令牌已由用户撤销。</span><span class="sxs-lookup"><span data-stu-id="b85df-189">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="b85df-190">当应用请求令牌时，有两种可能的结果：</span><span class="sxs-lookup"><span data-stu-id="b85df-190">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="b85df-191">请求成功，应用拥有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-191">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="b85df-192">请求失败，应用必须再次验证用户身份才能获得新令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-192">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="b85df-193">当令牌请求失败时，需要决定在执行重定向之前是否要保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-193">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="b85df-194">随着复杂程度的提高，可以使用以下几种方法：</span><span class="sxs-lookup"><span data-stu-id="b85df-194">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="b85df-195">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="b85df-195">Store the current page state in session storage.</span></span> <span data-ttu-id="b85df-196">在 [OnInitializedAsync 生命周期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期间，检查状态是否可以还原，然后再继续执行操作。</span><span class="sxs-lookup"><span data-stu-id="b85df-196">During the [OnInitializedAsync lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="b85df-197">添加查询字符串参数，并使用该参数向应用发送信号，而该应用需要重新水化先前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-197">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="b85df-198">添加具有唯一标识符的查询字符串参数以将数据存储在会话存储中，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="b85df-198">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="b85df-199">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="b85df-199">The following example shows how to:</span></span>

* <span data-ttu-id="b85df-200">在重定向到登录页面之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-200">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="b85df-201">使用查询字符串参数恢复先前经过身份验证后的状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-201">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="b85df-202">在身份验证操作之前保存应用状态</span><span class="sxs-lookup"><span data-stu-id="b85df-202">Save app state before an authentication operation</span></span>

<span data-ttu-id="b85df-203">在身份验证操作过程中，有时要在将浏览器重定向到 IP 之前保存应用状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-203">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="b85df-204">其中一种情况是使用状态容器并希望在身份验证成功后恢复状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-204">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="b85df-205">可以使用自定义身份验证状态对象来保留特定于应用的状态或对其的引用，并在身份验证操作成功完成期间恢复该状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-205">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="b85df-206">下面的示例演示了该方法。</span><span class="sxs-lookup"><span data-stu-id="b85df-206">The following example demonstrates the approach.</span></span>

<span data-ttu-id="b85df-207">在应用中创建状态容器类，该类具有用于保存应用的状态值的属性。</span><span class="sxs-lookup"><span data-stu-id="b85df-207">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="b85df-208">在以下示例中，容器用于维护默认模板的 `Counter` 组件 (Pages/Counter.razor\*\*) 的计数器值。</span><span class="sxs-lookup"><span data-stu-id="b85df-208">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="b85df-209">用于序列化和反序列化容器的方法基于 <xref:System.Text.Json>。</span><span class="sxs-lookup"><span data-stu-id="b85df-209">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="b85df-210">`Counter` 组件使用状态容器来维护组件以外的 `currentCount` 值：</span><span class="sxs-lookup"><span data-stu-id="b85df-210">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="b85df-211">从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 创建 `ApplicationAuthenticationState`。</span><span class="sxs-lookup"><span data-stu-id="b85df-211">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="b85df-212">提供 `Id` 属性，该属性用作本地存储状态的标识符。</span><span class="sxs-lookup"><span data-stu-id="b85df-212">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="b85df-213">ApplicationAuthenticationState.cs\*\*：</span><span class="sxs-lookup"><span data-stu-id="b85df-213">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="b85df-214">`Authentication` 组件 (Pages/Authentication.razor\*\*) 使用本地会话存储以及 `StateContainer` 序列化和反序列化方法（`GetStateForLocalStorage` 和 `SetStateFromLocalStorage`）保存和还原应用的状态：</span><span class="sxs-lookup"><span data-stu-id="b85df-214">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="b85df-215">本示例使用 Azure Active Directory (AAD) 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85df-215">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="b85df-216">在 `Program.Main` (Program.cs\*\*) 中：</span><span class="sxs-lookup"><span data-stu-id="b85df-216">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="b85df-217">将 `ApplicationAuthenticationState` 配置为 Microsoft 身份验证库 (MSAL)`RemoteAuthenticationState` 类型。</span><span class="sxs-lookup"><span data-stu-id="b85df-217">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="b85df-218">在服务容器中注册状态容器。</span><span class="sxs-lookup"><span data-stu-id="b85df-218">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="b85df-219">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="b85df-219">Customize app routes</span></span>

<span data-ttu-id="b85df-220">默认情况下，[Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 库使用下表中显示的路由表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-220">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="b85df-221">路由</span><span class="sxs-lookup"><span data-stu-id="b85df-221">Route</span></span>                            | <span data-ttu-id="b85df-222">目标</span><span class="sxs-lookup"><span data-stu-id="b85df-222">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="b85df-223">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="b85df-223">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="b85df-224">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="b85df-224">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="b85df-225">当登录操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="b85df-225">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="b85df-226">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="b85df-226">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="b85df-227">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="b85df-227">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="b85df-228">当注销操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="b85df-228">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="b85df-229">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="b85df-229">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="b85df-230">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="b85df-230">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="b85df-231">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="b85df-231">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="b85df-232">上表中显示的路由可通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="b85df-232">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b85df-233">设置选项以提供自定义路由时，确认应用具有可处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="b85df-233">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="b85df-234">在以下示例中，所有路径都带有 `/security` 前缀。</span><span class="sxs-lookup"><span data-stu-id="b85df-234">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="b85df-235">`Authentication` 组件 (Pages/Authentication.razor\*\*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="b85df-236">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-236">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b85df-237">如果需求要求使用完全不同的路径，请按照前面所述设置路由，并使用显式的操作参数呈现 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>：</span><span class="sxs-lookup"><span data-stu-id="b85df-237">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="b85df-238">如果选择这样做，则允许将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="b85df-238">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="b85df-239">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="b85df-239">Customize the authentication user interface</span></span>

<span data-ttu-id="b85df-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 包括每个身份验证状态的一组默认 UI 片段。</span><span class="sxs-lookup"><span data-stu-id="b85df-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="b85df-241">可以通过传入自定义 <xref:Microsoft.AspNetCore.Components.RenderFragment> 自定义每个状态。</span><span class="sxs-lookup"><span data-stu-id="b85df-241">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="b85df-242">要在初始登录过程中自定义显示的文本，可以按如下所示更改 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>。</span><span class="sxs-lookup"><span data-stu-id="b85df-242">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="b85df-243">`Authentication` 组件 (Pages/Authentication.razor\*\*)：</span><span class="sxs-lookup"><span data-stu-id="b85df-243">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="b85df-244">下表中显示了 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 的一个片段，该片段可用于每个身份验证路由。</span><span class="sxs-lookup"><span data-stu-id="b85df-244">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="b85df-245">路由</span><span class="sxs-lookup"><span data-stu-id="b85df-245">Route</span></span>                            | <span data-ttu-id="b85df-246">Fragment</span><span class="sxs-lookup"><span data-stu-id="b85df-246">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="b85df-247">自定义用户</span><span class="sxs-lookup"><span data-stu-id="b85df-247">Customize the user</span></span>

<span data-ttu-id="b85df-248">可自定义绑定到应用的用户。</span><span class="sxs-lookup"><span data-stu-id="b85df-248">Users bound to the app can be customized.</span></span> <span data-ttu-id="b85df-249">在以下示例中，所有经过身份验证的用户都会收到每种用户身份验证方法的 `amr` 声明。</span><span class="sxs-lookup"><span data-stu-id="b85df-249">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="b85df-250">创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 类的类：</span><span class="sxs-lookup"><span data-stu-id="b85df-250">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="b85df-251">创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 的中心：</span><span class="sxs-lookup"><span data-stu-id="b85df-251">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="b85df-252">为正在使用的验证提供程序注册 `CustomAccountFactory`。</span><span class="sxs-lookup"><span data-stu-id="b85df-252">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="b85df-253">以下任何注册均有效：</span><span class="sxs-lookup"><span data-stu-id="b85df-253">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="b85df-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>：</span><span class="sxs-lookup"><span data-stu-id="b85df-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="b85df-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>：</span><span class="sxs-lookup"><span data-stu-id="b85df-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="b85df-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>：</span><span class="sxs-lookup"><span data-stu-id="b85df-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="b85df-257">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="b85df-257">Support prerendering with authentication</span></span>

<span data-ttu-id="b85df-258">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="b85df-258">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="b85df-259">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="b85df-259">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="b85df-260">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="b85df-260">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="b85df-261">在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="b85df-261">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="b85df-262">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="b85df-262">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="b85df-263">在服务器应用的 `Startup.Configure` 方法中，将 [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 替换为 [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：</span><span class="sxs-lookup"><span data-stu-id="b85df-263">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="b85df-264">在服务器应用中，如果不存在 Pages\*\* 文件夹，则创建它。</span><span class="sxs-lookup"><span data-stu-id="b85df-264">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="b85df-265">在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。\*\* \*\*</span><span class="sxs-lookup"><span data-stu-id="b85df-265">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="b85df-266">将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。\*\* \*\*</span><span class="sxs-lookup"><span data-stu-id="b85df-266">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="b85df-267">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="b85df-267">Update the file's contents:</span></span>

* <span data-ttu-id="b85df-268">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="b85df-268">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="b85df-269">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b85df-269">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="b85df-270">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="b85df-270">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="b85df-271">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85df-271">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="b85df-272">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="b85df-272">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="b85df-273">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="b85df-273">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="b85df-274">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="b85df-274">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="b85df-275">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="b85df-275">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="b85df-276">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="b85df-276">In this scenario:</span></span>

* <span data-ttu-id="b85df-277">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="b85df-277">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="b85df-278">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="b85df-278">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="b85df-279">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="b85df-279">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="b85df-280">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="b85df-280">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="b85df-281">使用第三方登录提供程序配置 Identity。</span><span class="sxs-lookup"><span data-stu-id="b85df-281">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="b85df-282">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="b85df-282">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="b85df-283">当用户登录时，Identity 将在身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-283">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="b85df-284">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="b85df-284">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="b85df-285">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="b85df-285">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="b85df-286">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-286">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="b85df-287">在此处，使用第三方访问令牌直接从客户端上的 Identity 调用第三方 API 资源。</span><span class="sxs-lookup"><span data-stu-id="b85df-287">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="b85df-288">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="b85df-288">We don't recommend this approach.</span></span> <span data-ttu-id="b85df-289">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="b85df-289">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="b85df-290">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-290">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="b85df-291">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="b85df-291">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="b85df-292">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="b85df-292">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="b85df-293">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="b85df-293">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="b85df-294">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="b85df-294">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="b85df-295">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="b85df-295">Make an API call from the client to the server API.</span></span> <span data-ttu-id="b85df-296">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="b85df-296">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="b85df-297">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="b85df-297">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="b85df-298">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="b85df-298">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="b85df-299">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="b85df-299">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="b85df-300">使用 pen ID Connect (OIDC) v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="b85df-300">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="b85df-301">身份验证库和 Blazor 模板使用 Open ID Connect (OIDC) v1.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="b85df-301">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="b85df-302">要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="b85df-302">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="b85df-303">在下面的示例中，通过向 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> 属性追加 `v2.0` 段，将 AAD 配置为 v2.0：</span><span class="sxs-lookup"><span data-stu-id="b85df-303">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="b85df-304">也可以在应用设置 (appsettings.json\*\*) 文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="b85df-304">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="b85df-305">如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。</span><span class="sxs-lookup"><span data-stu-id="b85df-305">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="b85df-306">使用 `Authority` 键在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 或应用设置文件 (appsettings.json\*\*) 中设置属性。</span><span class="sxs-lookup"><span data-stu-id="b85df-306">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="b85df-307">ID 令牌中的声明列表针对 v2.0 终结点会发生更改。</span><span class="sxs-lookup"><span data-stu-id="b85df-307">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="b85df-308">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="b85df-308">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
