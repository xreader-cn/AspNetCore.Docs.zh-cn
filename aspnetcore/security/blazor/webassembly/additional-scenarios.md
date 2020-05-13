---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以实现其他安全方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153475"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="d898a-103">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="d898a-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="d898a-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="d898a-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="d898a-105">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="d898a-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="d898a-106">`AuthorizationMessageHandler`服务可用于 `HttpClient` 将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="d898a-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="d898a-107">使用现有服务获取令牌 `IAccessTokenProvider` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="d898a-108">如果无法获取令牌， `AccessTokenNotAvailableException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="d898a-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="d898a-109">`AccessTokenNotAvailableException`提供了一个 `Redirect` 方法，该方法可用于将用户导航到标识提供程序以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="d898a-110">`AuthorizationMessageHandler`使用方法，可以配置授权 url、范围和返回 URL `ConfigureHandler` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="d898a-111">在下面的示例中， `AuthorizationMessageHandler` `HttpClient` 在中配置 `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="d898a-112">为方便起见， `BaseAddressAuthorizationMessageHandler` 将包含的应用程序基址预配置为授权 URL。</span><span class="sxs-lookup"><span data-stu-id="d898a-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="d898a-113">启用身份验证的 Blazor WebAssembly 模板现在 <xref:System.Net.Http.IHttpClientFactory> 在服务器 API 项目中使用，以使用设置 <xref:System.Net.Http.HttpClient> `BaseAddressAuthorizationMessageHandler` ：</span><span class="sxs-lookup"><span data-stu-id="d898a-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="d898a-114">在前面的示例中使用创建客户端的位置提供了在 `CreateClient` <xref:System.Net.Http.HttpClient> 向服务器项目发出请求时提供访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="d898a-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="d898a-115">然后，将使用配置的 <xref:System.Net.Http.HttpClient> 来使用简单模式发出授权请求 `try-catch` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="d898a-116">`FetchData`组件（*Pages/FetchData*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-116">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="d898a-117">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="d898a-117">Typed HttpClient</span></span>

<span data-ttu-id="d898a-118">可以定义一个类型化客户端，用于处理单个类中的所有 HTTP 和标记获取问题。</span><span class="sxs-lookup"><span data-stu-id="d898a-118">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="d898a-119">*WeatherForecastClient.cs*：</span><span class="sxs-lookup"><span data-stu-id="d898a-119">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="d898a-120">`Program.Main`（*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-120">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="d898a-121">`FetchData`组件（*Pages/FetchData*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="d898a-122">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="d898a-122">Configure the HttpClient handler</span></span>

<span data-ttu-id="d898a-123">可以进一步 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="d898a-123">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="d898a-124">`Program.Main`（*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-124">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="d898a-125">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="d898a-125">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="d898a-126">如果 Blazor WebAssembly 应用程序通常使用安全默认值 <xref:System.Net.Http.HttpClient> ，则该应用程序还可以通过配置命名的来进行未经身份验证或未授权的 WEB API 请求 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="d898a-126">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="d898a-127">`Program.Main`（*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-127">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="d898a-128">除了现有的安全默认注册以外，以上注册也是如此 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="d898a-128">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="d898a-129">组件从创建， <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 以发出未经身份验证或未授权的请求：</span><span class="sxs-lookup"><span data-stu-id="d898a-129">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="d898a-130">对于前面的示例，服务器 API 中的控制器 `WeatherForecastNoAuthenticationController` 未标记为 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="d898a-130">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="d898a-131">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="d898a-131">Request additional access tokens</span></span>

<span data-ttu-id="d898a-132">可以通过调用手动获取访问令牌 `IAccessTokenProvider.RequestAccessToken` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-132">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="d898a-133">在下面的示例中，应用程序需要其他 Azure Active Directory （AAD） Microsoft Graph API 作用域来读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="d898a-133">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="d898a-134">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，会在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="d898a-134">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="d898a-135">`Program.Main`（*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-135">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="d898a-136">`IAccessTokenProvider.RequestToken`方法提供了一个重载，该重载允许应用程序使用一组给定的范围设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-136">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="d898a-137">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="d898a-137">In a Razor component:</span></span>

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

<span data-ttu-id="d898a-138">`TryGetToken`返回</span><span class="sxs-lookup"><span data-stu-id="d898a-138">`TryGetToken` returns:</span></span>

* <span data-ttu-id="d898a-139">`true``token`供使用的。</span><span class="sxs-lookup"><span data-stu-id="d898a-139">`true` with the `token` for use.</span></span>
* <span data-ttu-id="d898a-140">`false`如果未检索到标记，则为。</span><span class="sxs-lookup"><span data-stu-id="d898a-140">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="d898a-141">带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="d898a-141">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="d898a-142">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时， [HttpClient](xref:fundamentals/http-requests)并 <xref:System.Net.Http.HttpRequestMessage> 可用于自定义请求。</span><span class="sxs-lookup"><span data-stu-id="d898a-142">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="d898a-143">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="d898a-143">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="d898a-144">以下组件向 `POST` 服务器上的待办事项列表 API 终结点发出请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="d898a-144">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="d898a-145">.NET WebAssembly 的实现 `HttpClient` 使用[WindowOrWorkerGlobalScope （）](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="d898a-145">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="d898a-146">提取允许配置几个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="d898a-146">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="d898a-147">HTTP fetch 请求选项可配置 `HttpRequestMessage` 如下表中所示的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d898a-147">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="d898a-148">`HttpRequestMessage`extension 方法</span><span class="sxs-lookup"><span data-stu-id="d898a-148">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="d898a-149">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="d898a-149">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="d898a-150">凭据</span><span class="sxs-lookup"><span data-stu-id="d898a-150">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="d898a-151">区</span><span class="sxs-lookup"><span data-stu-id="d898a-151">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="d898a-152">mode</span><span class="sxs-lookup"><span data-stu-id="d898a-152">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="d898a-153">完整性</span><span class="sxs-lookup"><span data-stu-id="d898a-153">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="d898a-154">您可以使用更通用的扩展方法来设置其他选项 `SetBrowserRequestOption` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-154">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="d898a-155">HTTP 响应通常在 Blazor WebAssembly 应用中进行缓冲，以支持对响应内容进行同步读取。</span><span class="sxs-lookup"><span data-stu-id="d898a-155">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="d898a-156">若要启用对响应流的支持，请 `SetBrowserResponseStreamingEnabled` 对请求使用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d898a-156">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="d898a-157">若要在跨域请求中包含凭据，请使用 `SetBrowserRequestCredentials` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d898a-157">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="d898a-158">有关获取 API 选项的详细信息，请参阅[MDN web 文档： WindowOrWorkerGlobalScope （）:P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="d898a-158">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="d898a-159">在 CORS 请求上发送凭据（授权 cookie/标头）时， `Authorization` cors 策略必须允许标头。</span><span class="sxs-lookup"><span data-stu-id="d898a-159">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="d898a-160">以下策略包括的配置：</span><span class="sxs-lookup"><span data-stu-id="d898a-160">The following policy includes configuration for:</span></span>

* <span data-ttu-id="d898a-161">请求源（ `http://localhost:5000` 、 `https://localhost:5001` ）。</span><span class="sxs-lookup"><span data-stu-id="d898a-161">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="d898a-162">任何方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="d898a-162">Any method (verb).</span></span>
* <span data-ttu-id="d898a-163">`Content-Type`和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="d898a-163">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="d898a-164">若要允许自定义标头（例如 `x-custom-header` ），请在调用时列出标头 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。</span><span class="sxs-lookup"><span data-stu-id="d898a-164">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="d898a-165">由客户端 JavaScript 代码设置的凭据（ `credentials` 属性设置为 `include` ）。</span><span class="sxs-lookup"><span data-stu-id="d898a-165">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="d898a-166">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。</span><span class="sxs-lookup"><span data-stu-id="d898a-166">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="d898a-167">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="d898a-167">Handle token request errors</span></span>

<span data-ttu-id="d898a-168">在单页面应用程序（SPA）使用开放 ID Connect （OIDC）对用户进行身份验证时，身份验证状态以本地方式在 SPA 和标识提供程序（IP）中进行维护，其形式为用户提供其凭据的用户。</span><span class="sxs-lookup"><span data-stu-id="d898a-168">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="d898a-169">通常情况下，IP 为用户发出的令牌通常有效一小时，因此，客户端应用程序必须定期获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-169">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="d898a-170">否则，用户将在授予的令牌过期后注销。</span><span class="sxs-lookup"><span data-stu-id="d898a-170">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="d898a-171">在大多数情况下，OIDC 客户端可以预配新令牌，而无需用户重新进行身份验证，这是因为保留在 IP 中的身份验证状态或 "会话"。</span><span class="sxs-lookup"><span data-stu-id="d898a-171">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="d898a-172">在某些情况下，客户端在没有用户交互的情况下无法获取令牌，例如，由于某种原因，用户明确地从 IP 注销。</span><span class="sxs-lookup"><span data-stu-id="d898a-172">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="d898a-173">如果用户访问和注销，则会发生这种情况 `https://login.microsoftonline.com` 。在这些情况下，应用程序不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="d898a-173">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="d898a-174">此外，当当前令牌过期后，客户端将无法预配无用户交互的新令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-174">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="d898a-175">这些方案不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="d898a-175">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="d898a-176">它们是 Spa 的性质组成部分。</span><span class="sxs-lookup"><span data-stu-id="d898a-176">They are part of the nature of SPAs.</span></span> <span data-ttu-id="d898a-177">如果删除了身份验证 cookie，使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="d898a-177">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="d898a-178">当应用执行对受保护资源的 API 调用时，必须注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="d898a-178">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="d898a-179">若要预配新的访问令牌来调用 API，可能需要用户重新进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d898a-179">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="d898a-180">即使客户端具有看似有效的令牌，对服务器的调用可能会失败，因为该令牌已由用户吊销。</span><span class="sxs-lookup"><span data-stu-id="d898a-180">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="d898a-181">当应用请求令牌时，有两个可能的结果：</span><span class="sxs-lookup"><span data-stu-id="d898a-181">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="d898a-182">请求成功，并且应用具有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-182">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="d898a-183">请求失败，应用必须重新对用户进行身份验证才能获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-183">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="d898a-184">如果令牌请求失败，则需要确定是否要在执行重定向之前保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-184">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="d898a-185">增加复杂程度的方法有多种：</span><span class="sxs-lookup"><span data-stu-id="d898a-185">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="d898a-186">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="d898a-186">Store the current page state in session storage.</span></span> <span data-ttu-id="d898a-187">在期间 `OnInitializeAsync` ，请检查是否可以在继续操作之前还原状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-187">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="d898a-188">添加查询字符串参数，并使用该参数以向应用程序发出信号，指出需要重新冻结以前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-188">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="d898a-189">添加具有唯一标识符的查询字符串参数，以便在会话存储中存储数据，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="d898a-189">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="d898a-190">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="d898a-190">The following example shows how to:</span></span>

* <span data-ttu-id="d898a-191">在重定向到登录页之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-191">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="d898a-192">使用查询字符串参数恢复先前状态身份验证。</span><span class="sxs-lookup"><span data-stu-id="d898a-192">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="d898a-193">在身份验证操作之前保存应用程序状态</span><span class="sxs-lookup"><span data-stu-id="d898a-193">Save app state before an authentication operation</span></span>

<span data-ttu-id="d898a-194">在身份验证操作期间，某些情况下，你想要在将浏览器重定向到 IP 之前保存应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-194">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="d898a-195">当使用类似于状态容器的内容时，如果你想要在身份验证成功后还原状态，则可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="d898a-195">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="d898a-196">可以使用自定义身份验证状态对象来保持应用特定的状态或对其进行引用，并在身份验证操作成功完成后还原该状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-196">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="d898a-197">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-197">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="d898a-198">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="d898a-198">Customize app routes</span></span>

<span data-ttu-id="d898a-199">默认情况下， `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 该库使用下表中显示的路由来表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="d898a-199">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="d898a-200">路由</span><span class="sxs-lookup"><span data-stu-id="d898a-200">Route</span></span>                            | <span data-ttu-id="d898a-201">目的</span><span class="sxs-lookup"><span data-stu-id="d898a-201">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="d898a-202">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="d898a-202">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="d898a-203">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="d898a-203">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="d898a-204">当登录操作因某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="d898a-204">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="d898a-205">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="d898a-205">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="d898a-206">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="d898a-206">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="d898a-207">出于某些原因，在注销操作失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="d898a-207">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="d898a-208">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="d898a-208">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="d898a-209">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="d898a-209">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="d898a-210">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="d898a-210">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="d898a-211">可以通过配置上表中显示的路由 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-211">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="d898a-212">设置用于提供自定义路由的选项时，请确认该应用程序具有处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="d898a-212">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="d898a-213">在下面的示例中，所有路径都以为前缀 `/security` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-213">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="d898a-214">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-214">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="d898a-215">`Program.Main`（*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-215">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="d898a-216">如果要求调用完全不同的路径，请按前面所述设置路由，并 `RemoteAuthenticatorView` 使用显式操作参数呈现：</span><span class="sxs-lookup"><span data-stu-id="d898a-216">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="d898a-217">如果你选择这样做，则允许你将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="d898a-217">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="d898a-218">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="d898a-218">Customize the authentication user interface</span></span>

<span data-ttu-id="d898a-219">`RemoteAuthenticatorView`为每个身份验证状态包含一组默认的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="d898a-219">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="d898a-220">可以通过传入自定义来自定义每个状态 `RenderFragment` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-220">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="d898a-221">若要在初始登录过程中自定义显示的文本，可以更改，如下所示 `RemoteAuthenticatorView` 。</span><span class="sxs-lookup"><span data-stu-id="d898a-221">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="d898a-222">`Authentication`组件（*Pages/Authentication*）：</span><span class="sxs-lookup"><span data-stu-id="d898a-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="d898a-223">`RemoteAuthenticatorView`有一个可用于下表中所示的每个身份验证路由的片段。</span><span class="sxs-lookup"><span data-stu-id="d898a-223">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="d898a-224">路由</span><span class="sxs-lookup"><span data-stu-id="d898a-224">Route</span></span>                            | <span data-ttu-id="d898a-225">片段</span><span class="sxs-lookup"><span data-stu-id="d898a-225">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="d898a-226">自定义用户</span><span class="sxs-lookup"><span data-stu-id="d898a-226">Customize the user</span></span>

<span data-ttu-id="d898a-227">绑定到应用的用户可以自定义。</span><span class="sxs-lookup"><span data-stu-id="d898a-227">Users bound to the app can be customized.</span></span> <span data-ttu-id="d898a-228">在下面的示例中，所有经过身份验证的用户都 `amr` 将收到每个用户身份验证方法的声明。</span><span class="sxs-lookup"><span data-stu-id="d898a-228">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="d898a-229">创建扩展类的类 `RemoteUserAccount` ：</span><span class="sxs-lookup"><span data-stu-id="d898a-229">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="d898a-230">创建一个扩展的工厂 `AccountClaimsPrincipalFactory<TAccount>` ：</span><span class="sxs-lookup"><span data-stu-id="d898a-230">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="d898a-231">注册 `CustomAccountFactory` 要使用的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="d898a-231">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="d898a-232">以下任何注册都是有效的：</span><span class="sxs-lookup"><span data-stu-id="d898a-232">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="d898a-233">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d898a-233">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="d898a-234">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d898a-234">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="d898a-235">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="d898a-235">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="d898a-236">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="d898a-236">Support prerendering with authentication</span></span>

<span data-ttu-id="d898a-237">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="d898a-237">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="d898a-238">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="d898a-238">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="d898a-239">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="d898a-239">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="d898a-240">在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="d898a-240">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="d898a-241">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="d898a-241">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="d898a-242">在服务器应用的 `Startup.Configure` 方法中，将 `endpoints.MapFallbackToFile("index.html")` 替换为 `endpoints.MapFallbackToPage("/_Host")`：</span><span class="sxs-lookup"><span data-stu-id="d898a-242">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="d898a-243">在服务器应用中，如果不存在 Pages  文件夹，则创建它。</span><span class="sxs-lookup"><span data-stu-id="d898a-243">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="d898a-244">在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。  </span><span class="sxs-lookup"><span data-stu-id="d898a-244">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="d898a-245">将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。  </span><span class="sxs-lookup"><span data-stu-id="d898a-245">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="d898a-246">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="d898a-246">Update the file's contents:</span></span>

* <span data-ttu-id="d898a-247">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="d898a-247">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="d898a-248">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="d898a-248">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="d898a-249">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="d898a-249">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="d898a-250">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d898a-250">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="d898a-251">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="d898a-251">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="d898a-252">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="d898a-252">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="d898a-253">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="d898a-253">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="d898a-254">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="d898a-254">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="d898a-255">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="d898a-255">In this scenario:</span></span>

* <span data-ttu-id="d898a-256">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="d898a-256">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="d898a-257">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="d898a-257">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="d898a-258">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="d898a-258">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="d898a-259">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="d898a-259">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="d898a-260">Identity使用第三方登录提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="d898a-260">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="d898a-261">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="d898a-261">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="d898a-262">当用户登录时，会在 Identity 身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-262">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="d898a-263">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="d898a-263">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="d898a-264">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="d898a-264">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="d898a-265">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-265">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="d898a-266">在该处，使用第三方访问令牌直接从客户端调用第三方 API 资源 Identity 。</span><span class="sxs-lookup"><span data-stu-id="d898a-266">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="d898a-267">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="d898a-267">We don't recommend this approach.</span></span> <span data-ttu-id="d898a-268">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="d898a-268">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="d898a-269">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-269">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="d898a-270">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="d898a-270">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="d898a-271">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="d898a-271">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="d898a-272">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="d898a-272">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="d898a-273">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="d898a-273">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="d898a-274">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="d898a-274">Make an API call from the client to the server API.</span></span> <span data-ttu-id="d898a-275">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="d898a-275">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="d898a-276">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="d898a-276">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="d898a-277">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="d898a-277">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="d898a-278">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d898a-278">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
