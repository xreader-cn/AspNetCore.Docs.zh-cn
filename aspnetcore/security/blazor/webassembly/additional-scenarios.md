---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以实现其他安全方案。
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
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 35038cb7b96afd7c009f1210251e38273aa4aad8
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679652"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="08ddc-103">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="08ddc-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="08ddc-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="08ddc-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="08ddc-105">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="08ddc-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="08ddc-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>服务可用于 <xref:System.Net.Http.HttpClient> 将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="08ddc-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="08ddc-107">使用现有服务获取令牌 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="08ddc-108">如果无法获取令牌， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 则会引发。</span><span class="sxs-lookup"><span data-stu-id="08ddc-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="08ddc-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>提供了一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航到标识提供程序以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="08ddc-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>使用方法，可以配置授权 url、范围和返回 URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="08ddc-111">使用以下两种方法之一为传出请求配置消息处理程序：</span><span class="sxs-lookup"><span data-stu-id="08ddc-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="08ddc-112">[自定义 AuthorizationMessageHandler 类](#custom-authorizationmessagehandler-class)（*推荐*）</span><span class="sxs-lookup"><span data-stu-id="08ddc-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="08ddc-113">配置 AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="08ddc-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="08ddc-114">自定义 AuthorizationMessageHandler 类</span><span class="sxs-lookup"><span data-stu-id="08ddc-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="08ddc-115">在下面的示例中，自定义类扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> ，可用于配置 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="08ddc-116">在 `Program.Main` （*Program.cs*）中， <xref:System.Net.Http.HttpClient> 使用自定义授权消息处理程序配置了：</span><span class="sxs-lookup"><span data-stu-id="08ddc-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="08ddc-117">配置的 <xref:System.Net.Http.HttpClient> 用于使用[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)模式发出授权请求。</span><span class="sxs-lookup"><span data-stu-id="08ddc-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="08ddc-118">如果创建客户端时使用的是 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> （[Microsoft Extension. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)包）， <xref:System.Net.Http.HttpClient> 则提供的实例在向服务器 API 发出请求时包括访问令牌：</span><span class="sxs-lookup"><span data-stu-id="08ddc-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="08ddc-119">配置 AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="08ddc-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="08ddc-120">在下面的示例中， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> 在中配置 `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="08ddc-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="08ddc-121">为方便起见， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 将包含的应用程序基址预配置为授权 URL。</span><span class="sxs-lookup"><span data-stu-id="08ddc-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="08ddc-122">启用身份验证的 Blazor WebAssembly 模板现在使用 <xref:System.Net.Http.IHttpClientFactory> 服务器[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) API 项目中的（包）来设置 <xref:System.Net.Http.HttpClient> 具有的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="08ddc-123">在前面的示例中使用创建客户端的位置提供了在 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> 向服务器项目发出请求时提供访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="08ddc-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="08ddc-124">配置的 <xref:System.Net.Http.HttpClient> 用于使用[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)模式发出授权请求：</span><span class="sxs-lookup"><span data-stu-id="08ddc-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="08ddc-125">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="08ddc-125">Typed HttpClient</span></span>

<span data-ttu-id="08ddc-126">可以定义一个类型化客户端，用于处理单个类中的所有 HTTP 和标记获取问题。</span><span class="sxs-lookup"><span data-stu-id="08ddc-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="08ddc-127">WeatherForecastClient.cs：</span><span class="sxs-lookup"><span data-stu-id="08ddc-127">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="08ddc-128">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-128">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="08ddc-129">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-129">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="08ddc-130">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="08ddc-130">Configure the HttpClient handler</span></span>

<span data-ttu-id="08ddc-131">可以进一步 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="08ddc-131">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="08ddc-132">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="08ddc-133">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="08ddc-133">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="08ddc-134">如果 Blazor WebAssembly 应用程序通常使用安全默认值 <xref:System.Net.Http.HttpClient> ，则该应用程序还可以通过配置命名的来进行未经身份验证或未授权的 web API 请求 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-134">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="08ddc-135">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-135">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="08ddc-136">除了现有的安全默认注册以外，以上注册也是如此 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-136">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="08ddc-137">组件 <xref:System.Net.Http.HttpClient> 从 <xref:System.Net.Http.IHttpClientFactory> （[Microsoft extension. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)包）创建，以发出未经身份验证或未授权的请求：</span><span class="sxs-lookup"><span data-stu-id="08ddc-137">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="08ddc-138">对于前面的示例，服务器 API 中的控制器 `WeatherForecastNoAuthenticationController` 未标记为 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="08ddc-138">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="08ddc-139">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="08ddc-139">Request additional access tokens</span></span>

<span data-ttu-id="08ddc-140">可以通过调用手动获取访问令牌 `IAccessTokenProvider.RequestAccessToken` 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-140">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="08ddc-141">在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="08ddc-141">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="08ddc-142">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="08ddc-142">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="08ddc-143">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-143">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="08ddc-144">`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用一组给定的范围设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-144">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="08ddc-145">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="08ddc-145">In a Razor component:</span></span>

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

<span data-ttu-id="08ddc-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>返回</span><span class="sxs-lookup"><span data-stu-id="08ddc-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="08ddc-147">`true``token`供使用的。</span><span class="sxs-lookup"><span data-stu-id="08ddc-147">`true` with the `token` for use.</span></span>
* <span data-ttu-id="08ddc-148">`false`如果未检索到标记，则为。</span><span class="sxs-lookup"><span data-stu-id="08ddc-148">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="08ddc-149">带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="08ddc-149">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="08ddc-150">在 WebAssembly 应用程序的 WebAssembly 上运行时 Blazor ， [HttpClient](xref:fundamentals/http-requests)和 <xref:System.Net.Http.HttpRequestMessage> 可用于自定义请求。</span><span class="sxs-lookup"><span data-stu-id="08ddc-150">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="08ddc-151">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="08ddc-151">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="08ddc-152">以下组件向 `POST` 服务器上的待办事项列表 API 终结点发出请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="08ddc-152">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="08ddc-153">.NET WebAssembly 的实现 <xref:System.Net.Http.HttpClient> 使用[WindowOrWorkerGlobalScope （）](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="08ddc-153">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="08ddc-154">提取允许配置几个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="08ddc-154">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="08ddc-155">HTTP fetch 请求选项可配置 <xref:System.Net.Http.HttpRequestMessage> 如下表中所示的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="08ddc-155">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="08ddc-156">扩展方法</span><span class="sxs-lookup"><span data-stu-id="08ddc-156">Extension method</span></span> | <span data-ttu-id="08ddc-157">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="08ddc-157">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="08ddc-158">凭据</span><span class="sxs-lookup"><span data-stu-id="08ddc-158">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="08ddc-159">区</span><span class="sxs-lookup"><span data-stu-id="08ddc-159">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="08ddc-160">mode</span><span class="sxs-lookup"><span data-stu-id="08ddc-160">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="08ddc-161">完整性</span><span class="sxs-lookup"><span data-stu-id="08ddc-161">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="08ddc-162">您可以使用更通用的扩展方法来设置其他选项 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-162">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="08ddc-163">HTTP 响应通常在 Blazor WebAssembly 应用中进行缓冲，以支持对响应内容进行同步读取。</span><span class="sxs-lookup"><span data-stu-id="08ddc-163">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="08ddc-164">若要启用对响应流的支持，请 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 对请求使用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="08ddc-164">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="08ddc-165">若要在跨域请求中包含凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="08ddc-165">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="08ddc-166">有关获取 API 选项的详细信息，请参阅[MDN web 文档： WindowOrWorkerGlobalScope （）:P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="08ddc-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="08ddc-167">在 CORS 请求上发送凭据（授权 cookie/标头）时， `Authorization` cors 策略必须允许标头。</span><span class="sxs-lookup"><span data-stu-id="08ddc-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="08ddc-168">以下策略包括的配置：</span><span class="sxs-lookup"><span data-stu-id="08ddc-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="08ddc-169">请求源（ `http://localhost:5000` 、 `https://localhost:5001` ）。</span><span class="sxs-lookup"><span data-stu-id="08ddc-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="08ddc-170">任何方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="08ddc-170">Any method (verb).</span></span>
* <span data-ttu-id="08ddc-171">`Content-Type`和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="08ddc-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="08ddc-172">若要允许自定义标头（例如 `x-custom-header` ），请在调用时列出标头 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="08ddc-173">由客户端 JavaScript 代码设置的凭据（ `credentials` 属性设置为 `include` ）。</span><span class="sxs-lookup"><span data-stu-id="08ddc-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="08ddc-174">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。</span><span class="sxs-lookup"><span data-stu-id="08ddc-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="08ddc-175">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="08ddc-175">Handle token request errors</span></span>

<span data-ttu-id="08ddc-176">当单页面应用程序（SPA）使用 Open ID Connect （OIDC）对用户进行身份验证时，身份验证状态将 Identity 以会话 cookie （设置为用户提供凭据的用户的结果）的形式保留在 SPA 中的本地和提供程序（IP）中。</span><span class="sxs-lookup"><span data-stu-id="08ddc-176">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="08ddc-177">通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-177">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="08ddc-178">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="08ddc-178">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="08ddc-179">在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。</span><span class="sxs-lookup"><span data-stu-id="08ddc-179">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="08ddc-180">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。</span><span class="sxs-lookup"><span data-stu-id="08ddc-180">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="08ddc-181">如果用户访问和注销，则会发生这种情况 `https://login.microsoftonline.com` 。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="08ddc-181">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="08ddc-182">此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-182">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="08ddc-183">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="08ddc-183">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="08ddc-184">它们是 Spa 的性质组成部分。</span><span class="sxs-lookup"><span data-stu-id="08ddc-184">They are part of the nature of SPAs.</span></span> <span data-ttu-id="08ddc-185">如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="08ddc-185">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="08ddc-186">当应用执行对受保护资源的 API 调用时，必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="08ddc-186">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="08ddc-187">若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="08ddc-187">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="08ddc-188">即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。</span><span class="sxs-lookup"><span data-stu-id="08ddc-188">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="08ddc-189">当应用请求令牌时，有两个可能的结果：</span><span class="sxs-lookup"><span data-stu-id="08ddc-189">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="08ddc-190">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-190">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="08ddc-191">请求失败，应用必须重新对用户进行身份验证才能获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-191">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="08ddc-192">如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-192">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="08ddc-193">增加复杂程度的方法有多种：</span><span class="sxs-lookup"><span data-stu-id="08ddc-193">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="08ddc-194">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="08ddc-194">Store the current page state in session storage.</span></span> <span data-ttu-id="08ddc-195">在[OnInitializedAsync 生命周期事件](xref:blazor/lifecycle#component-initialization-methods)（ <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ）期间，请检查状态是否可以恢复，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="08ddc-195">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="08ddc-196">添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-196">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="08ddc-197">添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="08ddc-197">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="08ddc-198">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="08ddc-198">The following example shows how to:</span></span>

* <span data-ttu-id="08ddc-199">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-199">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="08ddc-200">使用查询字符串参数恢复先前状态身份验证。</span><span class="sxs-lookup"><span data-stu-id="08ddc-200">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="08ddc-201">在身份验证操作之前保存应用程序状态</span><span class="sxs-lookup"><span data-stu-id="08ddc-201">Save app state before an authentication operation</span></span>

<span data-ttu-id="08ddc-202">在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-202">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="08ddc-203">如果你使用状态容器，并且想要在身份验证成功后还原状态，则可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="08ddc-203">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="08ddc-204">你可以使用自定义身份验证状态对象来保留应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-204">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="08ddc-205">下面的示例演示了此方法。</span><span class="sxs-lookup"><span data-stu-id="08ddc-205">The following example demonstrates the approach.</span></span>

<span data-ttu-id="08ddc-206">在应用中创建一个状态容器类，其中包含用于保存应用状态值的属性。</span><span class="sxs-lookup"><span data-stu-id="08ddc-206">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="08ddc-207">在下面的示例中，容器用于维护默认模板的 `Counter` 组件（*Pages/counter*）的计数器值。</span><span class="sxs-lookup"><span data-stu-id="08ddc-207">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="08ddc-208">用于序列化和反序列化容器的方法基于 <xref:System.Text.Json> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-208">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="08ddc-209">`Counter`组件使用状态容器来维护 `currentCount` 组件以外的值：</span><span class="sxs-lookup"><span data-stu-id="08ddc-209">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="08ddc-210">从创建 `ApplicationAuthenticationState` 一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-210">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="08ddc-211">提供一个 `Id` 属性，该属性用作本地存储状态的标识符。</span><span class="sxs-lookup"><span data-stu-id="08ddc-211">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="08ddc-212">*ApplicationAuthenticationState.cs*：</span><span class="sxs-lookup"><span data-stu-id="08ddc-212">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="08ddc-213">`Authentication`组件（*Pages/Authentication*）通过使用序列化和反序列化方法将本地会话存储保存和还原应用的状态 `StateContainer` ， `GetStateForLocalStorage` 并 `SetStateFromLocalStorage` 执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="08ddc-213">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="08ddc-214">此示例使用 Azure Active Directory （AAD）进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="08ddc-214">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="08ddc-215">In `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="08ddc-215">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="08ddc-216">`ApplicationAuthenticationState`配置为 Microsoft 身份验证库（MSAL） `RemoteAuthenticationState` 类型。</span><span class="sxs-lookup"><span data-stu-id="08ddc-216">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="08ddc-217">状态容器在服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="08ddc-217">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="08ddc-218">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="08ddc-218">Customize app routes</span></span>

<span data-ttu-id="08ddc-219">默认情况下， [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="08ddc-219">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="08ddc-220">路由</span><span class="sxs-lookup"><span data-stu-id="08ddc-220">Route</span></span>                            | <span data-ttu-id="08ddc-221">目的</span><span class="sxs-lookup"><span data-stu-id="08ddc-221">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="08ddc-222">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="08ddc-222">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="08ddc-223">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="08ddc-223">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="08ddc-224">当登录操作因某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="08ddc-224">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="08ddc-225">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="08ddc-225">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="08ddc-226">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="08ddc-226">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="08ddc-227">出于某些原因，在注销操作失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="08ddc-227">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="08ddc-228">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="08ddc-228">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="08ddc-229">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="08ddc-229">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="08ddc-230">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="08ddc-230">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="08ddc-231">可以通过配置上表中显示的路由 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-231">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="08ddc-232">设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="08ddc-232">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="08ddc-233">在下面的示例中，所有路径都以为前缀 `/security` 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-233">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="08ddc-234">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="08ddc-234">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="08ddc-235">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-235">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="08ddc-236">如果要求调用完全不同的路径，请按前面所述设置路由，并 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 使用显式操作参数呈现：</span><span class="sxs-lookup"><span data-stu-id="08ddc-236">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="08ddc-237">如果你选择这样做，则允许你将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="08ddc-237">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="08ddc-238">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="08ddc-238">Customize the authentication user interface</span></span>

<span data-ttu-id="08ddc-239"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>为每个身份验证状态包含一组默认的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="08ddc-239"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="08ddc-240">可以通过传入自定义来自定义每个状态 <xref:Microsoft.AspNetCore.Components.RenderFragment> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-240">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="08ddc-241">若要在初始登录过程中自定义显示的文本，可以更改，如下所示 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-241">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="08ddc-242">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="08ddc-242">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="08ddc-243"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>有一个可用于下表中所示的每个身份验证路由的片段。</span><span class="sxs-lookup"><span data-stu-id="08ddc-243">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="08ddc-244">路由</span><span class="sxs-lookup"><span data-stu-id="08ddc-244">Route</span></span>                            | <span data-ttu-id="08ddc-245">片段</span><span class="sxs-lookup"><span data-stu-id="08ddc-245">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="08ddc-246">自定义用户</span><span class="sxs-lookup"><span data-stu-id="08ddc-246">Customize the user</span></span>

<span data-ttu-id="08ddc-247">绑定到应用的用户可以自定义。</span><span class="sxs-lookup"><span data-stu-id="08ddc-247">Users bound to the app can be customized.</span></span> <span data-ttu-id="08ddc-248">在下面的示例中，所有经过身份验证的用户都 `amr` 将收到每个用户身份验证方法的声明。</span><span class="sxs-lookup"><span data-stu-id="08ddc-248">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="08ddc-249">创建扩展类的类 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-249">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="08ddc-250">创建一个扩展的工厂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-250">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="08ddc-251">注册 `CustomAccountFactory` 要使用的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="08ddc-251">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="08ddc-252">以下任何注册都是有效的：</span><span class="sxs-lookup"><span data-stu-id="08ddc-252">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="08ddc-253"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="08ddc-253"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="08ddc-254"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="08ddc-254"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="08ddc-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="08ddc-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="08ddc-256">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="08ddc-256">Support prerendering with authentication</span></span>

<span data-ttu-id="08ddc-257">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="08ddc-257">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="08ddc-258">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="08ddc-258">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="08ddc-259">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="08ddc-259">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="08ddc-260">在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="08ddc-260">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="08ddc-261">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="08ddc-261">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="08ddc-262">在服务器应用程序的 `Startup.Configure` 方法中，将[终结点替换为。MapFallbackToFile （"index.html"）](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A)和[终结点。MapFallbackToPage （"/_Host"）](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：</span><span class="sxs-lookup"><span data-stu-id="08ddc-262">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="08ddc-263">在服务器应用中，如果不存在 Pages  文件夹，则创建它。</span><span class="sxs-lookup"><span data-stu-id="08ddc-263">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="08ddc-264">在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。  </span><span class="sxs-lookup"><span data-stu-id="08ddc-264">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="08ddc-265">将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。  </span><span class="sxs-lookup"><span data-stu-id="08ddc-265">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="08ddc-266">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="08ddc-266">Update the file's contents:</span></span>

* <span data-ttu-id="08ddc-267">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="08ddc-267">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="08ddc-268">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="08ddc-268">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="08ddc-269">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="08ddc-269">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="08ddc-270">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="08ddc-270">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="08ddc-271">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="08ddc-271">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="08ddc-272">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="08ddc-272">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="08ddc-273">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="08ddc-273">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="08ddc-274">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="08ddc-274">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="08ddc-275">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="08ddc-275">In this scenario:</span></span>

* <span data-ttu-id="08ddc-276">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="08ddc-276">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="08ddc-277">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="08ddc-277">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="08ddc-278">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="08ddc-278">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="08ddc-279">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="08ddc-279">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="08ddc-280">Identity使用第三方登录提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="08ddc-280">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="08ddc-281">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="08ddc-281">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="08ddc-282">当用户登录时，会在 Identity 身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-282">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="08ddc-283">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="08ddc-283">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="08ddc-284">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="08ddc-284">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="08ddc-285">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-285">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="08ddc-286">在该处，使用第三方访问令牌直接从客户端调用第三方 API 资源 Identity 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-286">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="08ddc-287">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="08ddc-287">We don't recommend this approach.</span></span> <span data-ttu-id="08ddc-288">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="08ddc-288">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="08ddc-289">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-289">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="08ddc-290">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="08ddc-290">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="08ddc-291">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="08ddc-291">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="08ddc-292">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="08ddc-292">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="08ddc-293">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="08ddc-293">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="08ddc-294">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="08ddc-294">Make an API call from the client to the server API.</span></span> <span data-ttu-id="08ddc-295">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="08ddc-295">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="08ddc-296">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="08ddc-296">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="08ddc-297">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="08ddc-297">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="08ddc-298">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="08ddc-298">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="08ddc-299">使用 Open ID Connect （OIDC） v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="08ddc-299">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="08ddc-300">身份验证库和 Blazor 模板使用 OPEN ID Connect （OIDC） v2.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="08ddc-300">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="08ddc-301">若要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="08ddc-301">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="08ddc-302">在下面的示例中，通过将一个段附加到属性，为 v2.0 配置了 AAD `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ：</span><span class="sxs-lookup"><span data-stu-id="08ddc-302">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="08ddc-303">或者，可以在应用设置（*appsettings.js*）文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="08ddc-303">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="08ddc-304">如果追踪的段上的 OIDC 不适合应用的提供程序（如使用非 AAD 提供程序），请 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接设置属性。</span><span class="sxs-lookup"><span data-stu-id="08ddc-304">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="08ddc-305">在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 应用程序设置文件（*appsettings.json*）中用键设置属性 `Authority` 。</span><span class="sxs-lookup"><span data-stu-id="08ddc-305">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="08ddc-306">对于 v2.0 终结点，ID 令牌中的声明列表会发生更改。</span><span class="sxs-lookup"><span data-stu-id="08ddc-306">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="08ddc-307">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台（v2.0）？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="08ddc-307">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
