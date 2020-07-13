---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: 了解如何为其他安全方案配置 Blazor WebAssembly。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 0cf2c2d2ef0d199ca5df6c27ddcc39e84db46ebd
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059755"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="14d40-103">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="14d40-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="14d40-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="14d40-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="14d40-105">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="14d40-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="14d40-106">可以将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 服务与 <xref:System.Net.Http.HttpClient> 一起使用，以将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="14d40-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="14d40-107">令牌是使用现有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 服务获取的。</span><span class="sxs-lookup"><span data-stu-id="14d40-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="14d40-108">如果无法获取令牌，则会引发 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>。</span><span class="sxs-lookup"><span data-stu-id="14d40-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="14d40-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航至标识提供者以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="14d40-110">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 方法将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 配置为授权的 URL、作用域和返回 URL。</span><span class="sxs-lookup"><span data-stu-id="14d40-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="14d40-111">使用以下两种方法之一为传出请求配置消息处理程序：</span><span class="sxs-lookup"><span data-stu-id="14d40-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="14d40-112">[自定义 `AuthorizationMessageHandler` 类](#custom-authorizationmessagehandler-class)（推荐）</span><span class="sxs-lookup"><span data-stu-id="14d40-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="14d40-113">配置 `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="14d40-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="14d40-114">自定义 AuthorizationMessageHandler 类</span><span class="sxs-lookup"><span data-stu-id="14d40-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="14d40-115">在下面的示例中，自定义类扩展了可用于配置 <xref:System.Net.Http.HttpClient> 的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>：</span><span class="sxs-lookup"><span data-stu-id="14d40-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="14d40-116">在 `Program.Main` (`Program.cs`) 中，使用自定义授权消息处理程序配置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="14d40-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="14d40-117">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配到 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="14d40-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="14d40-118">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求。</span><span class="sxs-lookup"><span data-stu-id="14d40-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="14d40-119">如果使用 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>（[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 包）创建客户端的情况，则在向服务器 API 发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例：</span><span class="sxs-lookup"><span data-stu-id="14d40-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="14d40-120">配置 AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="14d40-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="14d40-121">在下面的示例中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 在 `Program.Main` (`Program.cs`) 中配置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="14d40-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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
            BaseAddress = new Uri("https://www.example.com/base")
        };
});
```

<span data-ttu-id="14d40-122">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 分配到：</span><span class="sxs-lookup"><span data-stu-id="14d40-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="14d40-123"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="14d40-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="14d40-124">`authorizedUrls` 数组的 URL。</span><span class="sxs-lookup"><span data-stu-id="14d40-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="14d40-125">为了方便起见，包含 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>，它预先配置了应用基址作为授权的 URL。</span><span class="sxs-lookup"><span data-stu-id="14d40-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="14d40-126">支持身份验证的 Blazor WebAssembly 模板使用服务器 API 项目中的 <xref:System.Net.Http.IHttpClientFactory>（[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 包），通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 设置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="14d40-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="14d40-127">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配到 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="14d40-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="14d40-128">如果使用上一示例中的 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> 创建客户端，则在向服务器项目发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="14d40-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="14d40-129">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求：</span><span class="sxs-lookup"><span data-stu-id="14d40-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="14d40-130">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="14d40-130">Typed HttpClient</span></span>

<span data-ttu-id="14d40-131">可以定义类型化的客户端，以使用它处理单个类中的所有 HTTP 和令牌获取问题。</span><span class="sxs-lookup"><span data-stu-id="14d40-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="14d40-132">`WeatherForecastClient.cs`：</span><span class="sxs-lookup"><span data-stu-id="14d40-132">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="14d40-133">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `using static BlazorSample.Data;`）。</span><span class="sxs-lookup"><span data-stu-id="14d40-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="14d40-134">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="14d40-135">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配到 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="14d40-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="14d40-136">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="14d40-137">配置 HttpClient 处理程序</span><span class="sxs-lookup"><span data-stu-id="14d40-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="14d40-138">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求进一步配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="14d40-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="14d40-139">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="14d40-140">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 分配到：</span><span class="sxs-lookup"><span data-stu-id="14d40-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="14d40-141"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="14d40-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="14d40-142">`authorizedUrls` 数组的 URL。</span><span class="sxs-lookup"><span data-stu-id="14d40-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="14d40-143">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="14d40-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="14d40-144">如果 Blazor WebAssembly 应用通常使用安全的默认 <xref:System.Net.Http.HttpClient>，则该应用还可以通过配置命名的 <xref:System.Net.Http.HttpClient> 来发出未经身份验证或未经授权的 Web API 请求：</span><span class="sxs-lookup"><span data-stu-id="14d40-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="14d40-145">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="14d40-146">对于基于 Blazor WebAssembly 托管的模板的 Blazor 应用，可将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配到 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="14d40-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="14d40-147">先前的注册是对现有安全默认 <xref:System.Net.Http.HttpClient> 注册的补充。</span><span class="sxs-lookup"><span data-stu-id="14d40-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="14d40-148">组件从 <xref:System.Net.Http.IHttpClientFactory>（[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 包）创建 <xref:System.Net.Http.HttpClient>以发出未经身份验证或未经授权的请求：</span><span class="sxs-lookup"><span data-stu-id="14d40-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="14d40-149">服务器 API 中的控制器（上述示例中为 `WeatherForecastNoAuthenticationController`）未标记 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="14d40-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="14d40-150">开发人员决定使用安全客户端还是使用不安全的客户端作为默认 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="14d40-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="14d40-151">做出此决定的一种方法是，考虑应用联系的经过身份验证的终结点数与未经过身份验证的终结点数。</span><span class="sxs-lookup"><span data-stu-id="14d40-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="14d40-152">如果应用的大部分请求都要保护 API 终结点，请使用经过身份验证的 <xref:System.Net.Http.HttpClient> 实例作为默认实例。</span><span class="sxs-lookup"><span data-stu-id="14d40-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="14d40-153">否则，将未经身份验证的 <xref:System.Net.Http.HttpClient> 实例注册为默认实例。</span><span class="sxs-lookup"><span data-stu-id="14d40-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="14d40-154">使用 <xref:System.Net.Http.IHttpClientFactory> 的另一种方法是创建[类型化客户端](#typed-httpclient)，以便对匿名终结点进行未经身份验证的访问。</span><span class="sxs-lookup"><span data-stu-id="14d40-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="14d40-155">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="14d40-155">Request additional access tokens</span></span>

<span data-ttu-id="14d40-156">可以通过调用 `IAccessTokenProvider.RequestAccessToken` 手动获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="14d40-157">在以下示例中，应用需要其他 Azure Active Directory (AAD) Microsoft Graph API 作用域才能读取用户数据和发送邮件。</span><span class="sxs-lookup"><span data-stu-id="14d40-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="14d40-158">在 Azure AAD 门户中添加 Microsoft Graph API 权限后，将在客户端应用中配置其他作用域。</span><span class="sxs-lookup"><span data-stu-id="14d40-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="14d40-159">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-159">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="14d40-160">`IAccessTokenProvider.RequestToken` 方法提供重载，该重载允许应用使用一组给定的作用域来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="14d40-161">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="14d40-161">In a Razor component:</span></span>

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

<span data-ttu-id="14d40-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：</span><span class="sxs-lookup"><span data-stu-id="14d40-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="14d40-163">如果使用 `token`，则为 `true`。</span><span class="sxs-lookup"><span data-stu-id="14d40-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="14d40-164">如果未检索到令牌，则为 `false`。</span><span class="sxs-lookup"><span data-stu-id="14d40-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="14d40-165">具有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="14d40-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="14d40-166">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，可以使用 [`HttpClient`](xref:fundamentals/http-requests)（[API 文档](xref:System.Net.Http.HttpClient)）和 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。</span><span class="sxs-lookup"><span data-stu-id="14d40-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="14d40-167">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="14d40-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="14d40-168">以下组件向服务器上的待办事项列表 API 终结点发出 `POST` 请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="14d40-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="14d40-169"><xref:System.Net.Http.HttpClient> 的 .NET WebAssembly 实现使用 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="14d40-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="14d40-170">提取允许配置多个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="14d40-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="14d40-171">使用下表中所示的 <xref:System.Net.Http.HttpRequestMessage> 扩展方法可配置 HTTP fetch 请求选项。</span><span class="sxs-lookup"><span data-stu-id="14d40-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="14d40-172">扩展方法</span><span class="sxs-lookup"><span data-stu-id="14d40-172">Extension method</span></span> | <span data-ttu-id="14d40-173">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="14d40-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="14d40-174">可使用更通用的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 扩展方法设置其他选项。</span><span class="sxs-lookup"><span data-stu-id="14d40-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="14d40-175">HTTP 响应通常在 Blazor WebAssembly 应用中缓冲，以支持同步读取响应内容。</span><span class="sxs-lookup"><span data-stu-id="14d40-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="14d40-176">要支持响应流式处理，请对请求使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14d40-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="14d40-177">要在跨源请求中加入凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14d40-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="14d40-178">有关 Fetch API 选项的详细信息，请参阅 [MDN Web 文档：WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="14d40-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="14d40-179">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="14d40-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="14d40-180">在 CORS 请求中发送凭据（授权 cookie/标头）时，CORS 策略必须允许使用 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="14d40-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="14d40-181">以下策略包括的配置用于：</span><span class="sxs-lookup"><span data-stu-id="14d40-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="14d40-182">请求来源（`http://localhost:5000`、`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="14d40-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="14d40-183">Any 方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="14d40-183">Any method (verb).</span></span>
* <span data-ttu-id="14d40-184">`Content-Type` 和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="14d40-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="14d40-185">若要允许使用自定义标头（例如 `x-custom-header`），请在调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 时列出标头。</span><span class="sxs-lookup"><span data-stu-id="14d40-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="14d40-186">由客户端 JavaScript 代码设置的凭据（`credentials` 属性设置为 `include`）。</span><span class="sxs-lookup"><span data-stu-id="14d40-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="14d40-187">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试程序组件 (`Components/HTTPRequestTester.razor`)。</span><span class="sxs-lookup"><span data-stu-id="14d40-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="14d40-188">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="14d40-188">Handle token request errors</span></span>

<span data-ttu-id="14d40-189">当单页应用程序 (SPA) 使用 Open ID Connect (OIDC) 对用户进行身份验证时，身份验证状态将以会话 cookie（因用户提供其凭据而设置）的形式在 SPA 内和 Identity 提供者 (IP) 中在本地进行维护。</span><span class="sxs-lookup"><span data-stu-id="14d40-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="14d40-190">IP 为用户发出的令牌通常在短时间（约 1 小时）内有效，因此客户端应用必须定期提取新令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="14d40-191">否则，在授予的令牌到期后，将注销用户。</span><span class="sxs-lookup"><span data-stu-id="14d40-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="14d40-192">在大多数情况下，由于身份验证状态或“会话”保留在 IP 中，因此 OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="14d40-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="14d40-193">在某些情况下，如果没有用户交互，客户端就无法获得令牌（例如，当用户出于某种原因而明确从 IP 注销时）。</span><span class="sxs-lookup"><span data-stu-id="14d40-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="14d40-194">如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些场景下，应用不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="14d40-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="14d40-195">另外，当前令牌过期后，如果没有用户交互，客户端将无法预配新令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="14d40-196">这些场景并不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="14d40-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="14d40-197">它们本就是 SPA 的一部分。</span><span class="sxs-lookup"><span data-stu-id="14d40-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="14d40-198">如果删除了身份验证 cookie，则使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="14d40-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="14d40-199">当应用对受保护的资源执行 API 调用时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="14d40-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="14d40-200">要预配新的访问令牌来调用 API，可能需要用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="14d40-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="14d40-201">即使客户端拥有看似有效的令牌，对服务器的调用也可能会失败，因为该令牌已由用户撤销。</span><span class="sxs-lookup"><span data-stu-id="14d40-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="14d40-202">当应用请求令牌时，有两种可能的结果：</span><span class="sxs-lookup"><span data-stu-id="14d40-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="14d40-203">请求成功，应用拥有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="14d40-204">请求失败，应用必须再次验证用户身份才能获得新令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="14d40-205">当令牌请求失败时，需要决定在执行重定向之前是否要保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="14d40-206">随着复杂程度的提高，可以使用以下几种方法：</span><span class="sxs-lookup"><span data-stu-id="14d40-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="14d40-207">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="14d40-207">Store the current page state in session storage.</span></span> <span data-ttu-id="14d40-208">在 [`OnInitializedAsync` 生命周期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期间，检查状态是否可以还原，然后再继续执行操作。</span><span class="sxs-lookup"><span data-stu-id="14d40-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="14d40-209">添加查询字符串参数，并使用该参数向应用发送信号，而该应用需要重新水化先前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="14d40-210">添加具有唯一标识符的查询字符串参数以将数据存储在会话存储中，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="14d40-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="14d40-211">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="14d40-211">The following example shows how to:</span></span>

* <span data-ttu-id="14d40-212">在重定向到登录页面之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="14d40-213">使用查询字符串参数恢复先前经过身份验证后的状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="14d40-214">在身份验证操作之前保存应用状态</span><span class="sxs-lookup"><span data-stu-id="14d40-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="14d40-215">在身份验证操作过程中，有时要在将浏览器重定向到 IP 之前保存应用状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="14d40-216">其中一种情况是使用状态容器并希望在身份验证成功后恢复状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="14d40-217">可以使用自定义身份验证状态对象来保留特定于应用的状态或对其的引用，并在身份验证操作成功完成期间恢复该状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="14d40-218">下面的示例演示了该方法。</span><span class="sxs-lookup"><span data-stu-id="14d40-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="14d40-219">在应用中创建状态容器类，该类具有用于保存应用的状态值的属性。</span><span class="sxs-lookup"><span data-stu-id="14d40-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="14d40-220">在以下示例中，容器用于维护默认模板的 `Counter` 组件 (`Pages/Counter.razor`) 的计数器值。</span><span class="sxs-lookup"><span data-stu-id="14d40-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="14d40-221">用于序列化和反序列化容器的方法基于 <xref:System.Text.Json>。</span><span class="sxs-lookup"><span data-stu-id="14d40-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="14d40-222">`Counter` 组件使用状态容器来维护组件以外的 `currentCount` 值：</span><span class="sxs-lookup"><span data-stu-id="14d40-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="14d40-223">从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 创建 `ApplicationAuthenticationState`。</span><span class="sxs-lookup"><span data-stu-id="14d40-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="14d40-224">提供 `Id` 属性，该属性用作本地存储状态的标识符。</span><span class="sxs-lookup"><span data-stu-id="14d40-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="14d40-225">`ApplicationAuthenticationState.cs`：</span><span class="sxs-lookup"><span data-stu-id="14d40-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="14d40-226">`Authentication` 组件 (`Pages/Authentication.razor`) 使用本地会话存储以及 `StateContainer` 序列化和反序列化方法（`GetStateForLocalStorage` 和 `SetStateFromLocalStorage`）保存和还原应用的状态：</span><span class="sxs-lookup"><span data-stu-id="14d40-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="14d40-227">本示例使用 Azure Active Directory (AAD) 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="14d40-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="14d40-228">在 `Program.Main` (`Program.cs`) 中：</span><span class="sxs-lookup"><span data-stu-id="14d40-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="14d40-229">将 `ApplicationAuthenticationState` 配置为 Microsoft 身份验证库 (MSAL)`RemoteAuthenticationState` 类型。</span><span class="sxs-lookup"><span data-stu-id="14d40-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="14d40-230">在服务容器中注册状态容器。</span><span class="sxs-lookup"><span data-stu-id="14d40-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="14d40-231">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="14d40-231">Customize app routes</span></span>

<span data-ttu-id="14d40-232">默认情况下，[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 库使用下表中显示的路由表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="14d40-233">路由</span><span class="sxs-lookup"><span data-stu-id="14d40-233">Route</span></span>                            | <span data-ttu-id="14d40-234">目标</span><span class="sxs-lookup"><span data-stu-id="14d40-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="14d40-235">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="14d40-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="14d40-236">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="14d40-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="14d40-237">当登录操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="14d40-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="14d40-238">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="14d40-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="14d40-239">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="14d40-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="14d40-240">当注销操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="14d40-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="14d40-241">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="14d40-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="14d40-242">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="14d40-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="14d40-243">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="14d40-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="14d40-244">上表中显示的路由可通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="14d40-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="14d40-245">设置选项以提供自定义路由时，确认应用具有可处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="14d40-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="14d40-246">在以下示例中，所有路径都带有 `/security` 前缀。</span><span class="sxs-lookup"><span data-stu-id="14d40-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="14d40-247">`Authentication` 组件 (`Pages/Authentication.razor`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="14d40-248">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-248">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="14d40-249">如果需求要求使用完全不同的路径，请按照前面所述设置路由，并使用显式的操作参数呈现 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>：</span><span class="sxs-lookup"><span data-stu-id="14d40-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="14d40-250">如果选择这样做，则允许将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="14d40-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="14d40-251">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="14d40-251">Customize the authentication user interface</span></span>

<span data-ttu-id="14d40-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 包括每个身份验证状态的一组默认 UI 片段。</span><span class="sxs-lookup"><span data-stu-id="14d40-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="14d40-253">可以通过传入自定义 <xref:Microsoft.AspNetCore.Components.RenderFragment> 自定义每个状态。</span><span class="sxs-lookup"><span data-stu-id="14d40-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="14d40-254">要在初始登录过程中自定义显示的文本，可以按如下所示更改 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>。</span><span class="sxs-lookup"><span data-stu-id="14d40-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="14d40-255">`Authentication` 组件 (`Pages/Authentication.razor`)：</span><span class="sxs-lookup"><span data-stu-id="14d40-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="14d40-256">下表中显示了 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 的一个片段，该片段可用于每个身份验证路由。</span><span class="sxs-lookup"><span data-stu-id="14d40-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="14d40-257">路由</span><span class="sxs-lookup"><span data-stu-id="14d40-257">Route</span></span>                            | <span data-ttu-id="14d40-258">Fragment</span><span class="sxs-lookup"><span data-stu-id="14d40-258">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="14d40-259">自定义用户</span><span class="sxs-lookup"><span data-stu-id="14d40-259">Customize the user</span></span>

<span data-ttu-id="14d40-260">可自定义绑定到应用的用户。</span><span class="sxs-lookup"><span data-stu-id="14d40-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="14d40-261">在以下示例中，所有经过身份验证的用户都会收到每种用户身份验证方法的 `amr` 声明。</span><span class="sxs-lookup"><span data-stu-id="14d40-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="14d40-262">创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 类的类：</span><span class="sxs-lookup"><span data-stu-id="14d40-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="14d40-263">创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 的中心：</span><span class="sxs-lookup"><span data-stu-id="14d40-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="14d40-264">为正在使用的验证提供程序注册 `CustomAccountFactory`。</span><span class="sxs-lookup"><span data-stu-id="14d40-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="14d40-265">以下任何注册均有效：</span><span class="sxs-lookup"><span data-stu-id="14d40-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="14d40-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>：</span><span class="sxs-lookup"><span data-stu-id="14d40-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="14d40-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>：</span><span class="sxs-lookup"><span data-stu-id="14d40-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="14d40-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>：</span><span class="sxs-lookup"><span data-stu-id="14d40-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="14d40-269">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="14d40-269">Support prerendering with authentication</span></span>

<span data-ttu-id="14d40-270">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="14d40-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="14d40-271">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="14d40-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="14d40-272">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="14d40-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="14d40-273">在客户端应用的 `Program` 类 (`Program.cs`) 中，将常见服务注册纳入单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="14d40-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="14d40-274">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="14d40-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="14d40-275">在 Server 应用的 `Startup.Configure` 方法中，将 [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 替换为 [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：</span><span class="sxs-lookup"><span data-stu-id="14d40-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="14d40-276">在 Server 应用中，如果不存在 `Pages` 文件夹，请创建一个。</span><span class="sxs-lookup"><span data-stu-id="14d40-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="14d40-277">在 Server 应用的 `Pages` 文件夹中创建 `_Host.cshtml` 页。</span><span class="sxs-lookup"><span data-stu-id="14d40-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="14d40-278">将客户端应用 `wwwroot/index.html` 文件中的内容粘贴到 `Pages/_Host.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="14d40-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="14d40-279">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="14d40-279">Update the file's contents:</span></span>

* <span data-ttu-id="14d40-280">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="14d40-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="14d40-281">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="14d40-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="14d40-282">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="14d40-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="14d40-283">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="14d40-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="14d40-284">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="14d40-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="14d40-285">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="14d40-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="14d40-286">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="14d40-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="14d40-287">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="14d40-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="14d40-288">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="14d40-288">In this scenario:</span></span>

* <span data-ttu-id="14d40-289">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="14d40-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="14d40-290">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="14d40-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="14d40-291">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="14d40-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="14d40-292">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="14d40-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="14d40-293">使用第三方登录提供程序配置 Identity。</span><span class="sxs-lookup"><span data-stu-id="14d40-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="14d40-294">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="14d40-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="14d40-295">当用户登录时，Identity 将在身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="14d40-296">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="14d40-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="14d40-297">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="14d40-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="14d40-298">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="14d40-299">在此处，使用第三方访问令牌直接从客户端上的 Identity 调用第三方 API 资源。</span><span class="sxs-lookup"><span data-stu-id="14d40-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="14d40-300">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="14d40-300">We don't recommend this approach.</span></span> <span data-ttu-id="14d40-301">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="14d40-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="14d40-302">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="14d40-303">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="14d40-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="14d40-304">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="14d40-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="14d40-305">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="14d40-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="14d40-306">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="14d40-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="14d40-307">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="14d40-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="14d40-308">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="14d40-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="14d40-309">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="14d40-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="14d40-310">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="14d40-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="14d40-311">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14d40-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="14d40-312">使用 pen ID Connect (OIDC) v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="14d40-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="14d40-313">身份验证库和 Blazor 模板使用 Open ID Connect (OIDC) v1.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="14d40-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="14d40-314">要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="14d40-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="14d40-315">在下面的示例中，通过向 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> 属性追加 `v2.0` 段，将 AAD 配置为 v2.0：</span><span class="sxs-lookup"><span data-stu-id="14d40-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="14d40-316">也可以在应用设置 (`appsettings.json`) 文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="14d40-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="14d40-317">如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。</span><span class="sxs-lookup"><span data-stu-id="14d40-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="14d40-318">使用 `Authority` 键在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 或应用设置文件 (`appsettings.json`) 中设置属性。</span><span class="sxs-lookup"><span data-stu-id="14d40-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="14d40-319">ID 令牌中的声明列表针对 v2.0 终结点会发生更改。</span><span class="sxs-lookup"><span data-stu-id="14d40-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="14d40-320">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="14d40-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
