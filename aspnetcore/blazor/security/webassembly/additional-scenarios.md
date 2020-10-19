---
title: ASP.NET Core Blazor WebAssembly 其他安全方案
author: guardrex
description: 了解如何为其他安全方案配置 Blazor WebAssembly。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 50d455b37c51fdd6d3b52b10b3e819eb45526de4
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900955"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="3e5c2-103">ASP.NET Core Blazor WebAssembly 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="3e5c2-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="3e5c2-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3e5c2-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="3e5c2-105">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="3e5c2-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="3e5c2-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 是一个 <xref:System.Net.Http.DelegatingHandler>，用于将访问令牌附加到传出 <xref:System.Net.Http.HttpResponseMessage> 实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="3e5c2-107">令牌是使用由框架注册的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 服务获取的。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="3e5c2-108">如果无法获取令牌，则会引发 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="3e5c2-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，该方法可用于将用户导航至标识提供者以获取新令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="3e5c2-110">为了方便起见，框架提供 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>，它预先配置了应用基址作为授权的 URL。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="3e5c2-111">仅当请求 URI 在应用的基 URI 中时，才会添加访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="3e5c2-112">当传出请求 URI 不在应用的基 URI 中时，请使用[自定义 `AuthorizationMessageHandler` 类（推荐）](#custom-authorizationmessagehandler-class)或[配置 `AuthorizationMessageHandler`](#configure-authorizationmessagehandler)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="3e5c2-113">除了用于服务器 API 访问的客户端应用配置之外，在客户端和服务器不位于同一基址时，服务器 API 还必须允许跨域请求 (CORS)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="3e5c2-114">有关服务器端 CORS 配置的详细信息，请参阅本文后面的[跨域资源共享 (CORS)](#cross-origin-resource-sharing-cors) 部分。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="3e5c2-115">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-115">In the following example:</span></span>

* <span data-ttu-id="3e5c2-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> 将 <xref:System.Net.Http.IHttpClientFactory> 和相关服务添加到服务集合中，并配置已命名的 <xref:System.Net.Http.HttpClient> (`ServerAPI`)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="3e5c2-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 是发送请求时资源 URI 的基址。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="3e5c2-118"><xref:System.Net.Http.IHttpClientFactory> 由 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet 包提供。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="3e5c2-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 是用于将访问令牌附加到传出 <xref:System.Net.Http.HttpResponseMessage> 实例的 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="3e5c2-120">仅当请求 URI 在应用的基 URI 中时，才会添加访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="3e5c2-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> 使用与已命名的 <xref:System.Net.Http.HttpClient> (`ServerAPI`) 相对应的配置来创建和配置 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="3e5c2-122">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，请求 URI 位于应用的基 URI 内。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="3e5c2-123">因此，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 被分配给从项目模板生成的应用中的 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="3e5c2-124">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="3e5c2-125">自定义 `AuthorizationMessageHandler` 类</span><span class="sxs-lookup"><span data-stu-id="3e5c2-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="3e5c2-126">如果向 URI 发出传出请求的客户端应用不在应用的基 URI 内，建议使用本部分中的指南。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="3e5c2-127">在下面的示例中，一个自定义类扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 以用作 <xref:System.Net.Http.HttpClient> 的 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="3e5c2-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 配置此处理程序，以使用访问令牌授权出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="3e5c2-129">仅当至少有一个授权 URL 是请求 URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) 的基 URI 时，才附加访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="3e5c2-130">在 `Program.Main` (`Program.cs`) 中，`CustomAuthorizationMessageHandler` 被注册为一个作用域内的服务，并被配置为由已命名的 <xref:System.Net.Http.HttpClient> 发出的传出 <xref:System.Net.Http.HttpResponseMessage> 实例的 <xref:System.Net.Http.DelegatingHandler>：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="3e5c2-131">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配给 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3e5c2-132">配置的 <xref:System.Net.Http.HttpClient> 用于使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 模式发出授权的请求。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="3e5c2-133">其中使用 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>（[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 包）创建客户端，在向服务器 API 发出请求时，将向 <xref:System.Net.Http.HttpClient> 提供包含访问令牌的实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="3e5c2-134">如果请求 URI 是相对 URI，如以下示例 (`ExampleAPIMethod`) 中所示，则当客户端应用发出请求时，它将与 <xref:System.Net.Http.HttpClient.BaseAddress> 结合使用：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="3e5c2-135">配置 `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="3e5c2-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="3e5c2-136">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 方法将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 配置为授权的 URL、作用域和返回 URL。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="3e5c2-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 配置此处理程序，以使用访问令牌授权出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="3e5c2-138">仅当至少有一个授权 URL 是请求 URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) 的基 URI 时，才附加访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="3e5c2-139">如果请求 URI 是相对 URI，则将与 <xref:System.Net.Http.HttpClient.BaseAddress> 结合使用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="3e5c2-140">在下面的示例中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 在 `Program.Main` (`Program.cs`) 中配置 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="3e5c2-141">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 分配给以下地址：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="3e5c2-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="3e5c2-143">`authorizedUrls` 数组的 URL。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-143">A URL of the `authorizedUrls` array.</span></span>

### <a name="graph-api-example"></a><span data-ttu-id="3e5c2-144">图形 API 示例</span><span class="sxs-lookup"><span data-stu-id="3e5c2-144">Graph API example</span></span>

<span data-ttu-id="3e5c2-145">在下面的示例中，使用图形 API 的命名 <xref:System.Net.Http.HttpClient> 获取用户的移动电话号码以处理调用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-145">In the following example, a named <xref:System.Net.Http.HttpClient> for Graph API is used to obtain a user's mobile phone number to process a call.</span></span> <span data-ttu-id="3e5c2-146">在 Azure 门户的 AAD 区域中添加 Microsoft Graph API `User.Read` 权限后，将为独立应用或托管 Blazor 解决方案的 `Client` 应用中的命名客户端配置作用域。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-146">After adding the Microsoft Graph API `User.Read` permission in the AAD area of the Azure portal, the scope is configured for the named client in the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>

> [!NOTE]
> <span data-ttu-id="3e5c2-147">本部分中的示例获取组件代码中用户的图形 API 数据。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-147">The example in this section obtains Graph API data for the user in *component code*.</span></span> <span data-ttu-id="3e5c2-148">若要根据图形 API 创建用户声明，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-148">To create user claims from Graph API, see the following resources:</span></span>
>
> * <span data-ttu-id="3e5c2-149">[自定义用户](#customize-the-user)部分</span><span class="sxs-lookup"><span data-stu-id="3e5c2-149">[Customize the user](#customize-the-user) section</span></span>
> * <xref:blazor/security/webassembly/aad-groups-roles>

<span data-ttu-id="3e5c2-150">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-150">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="3e5c2-151">在 `Program.Main` (`Program.cs`) 中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-151">In `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="3e5c2-152">在 Razor 组件 (`Pages/CallUser.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-152">In a Razor component (`Pages/CallUser.razor`):</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="3e5c2-153">在前面的示例中，开发人员实现了自定义的 `ICallProcessor` (`CallProcessor`)，以便对自动调用进行排队和放置。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-153">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="3e5c2-154">类型化 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="3e5c2-154">Typed `HttpClient`</span></span>

<span data-ttu-id="3e5c2-155">可以定义类型化的客户端，以使用它处理单个类中的所有 HTTP 和令牌获取问题。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-155">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="3e5c2-156">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-156">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="3e5c2-157">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `using static BlazorSample.Data;`）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-157">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="3e5c2-158">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3e5c2-158">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="3e5c2-159">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配给 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-159">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3e5c2-160">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="3e5c2-161">配置 `HttpClient` 处理程序</span><span class="sxs-lookup"><span data-stu-id="3e5c2-161">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="3e5c2-162">可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 为出站 HTTP 请求进一步配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-162">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="3e5c2-163">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3e5c2-163">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="3e5c2-164">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 分配给以下地址：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-164">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="3e5c2-165"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-165">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="3e5c2-166">`authorizedUrls` 数组的 URL。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-166">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="3e5c2-167">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="3e5c2-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="3e5c2-168">如果 Blazor WebAssembly 应用通常使用安全的默认 <xref:System.Net.Http.HttpClient>，则该应用还可以通过配置命名的 <xref:System.Net.Http.HttpClient> 来发出未经身份验证或未经授权的 Web API 请求：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-168">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="3e5c2-169">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3e5c2-169">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="3e5c2-170">对于基于 Blazor WebAssembly 托管项目模板的 Blazor 应用，默认情况下，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 分配给 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-170">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3e5c2-171">先前的注册是对现有安全默认 <xref:System.Net.Http.HttpClient> 注册的补充。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-171">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="3e5c2-172">组件从 <xref:System.Net.Http.IHttpClientFactory>（[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 包）创建 <xref:System.Net.Http.HttpClient>以发出未经身份验证或未经授权的请求：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-172">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="3e5c2-173">服务器 API 中的控制器（上述示例中为 `WeatherForecastNoAuthenticationController`）未标记 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-173">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="3e5c2-174">开发人员决定使用安全客户端还是使用不安全的客户端作为默认 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-174">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="3e5c2-175">做出此决定的一种方法是，考虑应用联系的经过身份验证的终结点数与未经过身份验证的终结点数。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-175">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="3e5c2-176">如果应用的大部分请求都要保护 API 终结点，请使用经过身份验证的 <xref:System.Net.Http.HttpClient> 实例作为默认实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-176">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="3e5c2-177">否则，将未经身份验证的 <xref:System.Net.Http.HttpClient> 实例注册为默认实例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-177">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="3e5c2-178">使用 <xref:System.Net.Http.IHttpClientFactory> 的另一种方法是创建[类型化客户端](#typed-httpclient)，以便对匿名终结点进行未经身份验证的访问。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-178">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="3e5c2-179">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="3e5c2-179">Request additional access tokens</span></span>

<span data-ttu-id="3e5c2-180">可以通过调用 `IAccessTokenProvider.RequestAccessToken` 手动获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-180">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="3e5c2-181">在下面的示例中，应用需要一个额外的作用域作为默认 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-181">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="3e5c2-182">Microsoft 身份验证库 (MSAL) 示例使用 `MsalProviderOptions` 配置作用域：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-182">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="3e5c2-183">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3e5c2-183">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="3e5c2-184">上例中的 `{CUSTOM SCOPE 1}` 和 `{CUSTOM SCOPE 2}` 占位符是自定义作用域。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-184">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="3e5c2-185">`IAccessTokenProvider.RequestToken` 方法提供重载，该重载允许应用使用一组给定的作用域来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-185">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="3e5c2-186">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-186">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="3e5c2-187">上例中的 `{CUSTOM SCOPE 1}` 和 `{CUSTOM SCOPE 2}` 占位符是自定义作用域。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-187">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="3e5c2-188"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-188"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="3e5c2-189">如果使用 `token`，则为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-189">`true` with the `token` for use.</span></span>
* <span data-ttu-id="3e5c2-190">如果未检索到令牌，则为 `false`。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-190">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="3e5c2-191">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="3e5c2-191">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="3e5c2-192">在 CORS 请求中发送凭据（授权 cookie/标头）时，CORS 策略必须允许使用 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-192">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="3e5c2-193">以下策略包括的配置用于：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-193">The following policy includes configuration for:</span></span>

* <span data-ttu-id="3e5c2-194">请求来源（`http://localhost:5000`、`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-194">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="3e5c2-195">Any 方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-195">Any method (verb).</span></span>
* <span data-ttu-id="3e5c2-196">`Content-Type` 和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-196">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="3e5c2-197">若要允许使用自定义标头（例如 `x-custom-header`），请在调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 时列出标头。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-197">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="3e5c2-198">由客户端 JavaScript 代码设置的凭据（`credentials` 属性设置为 `include`）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-198">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="3e5c2-199">基于 Blazor 托管的项目模板的托管 Blazor 解决方案对客户端和服务器应用使用相同的基址。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-199">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="3e5c2-200">默认情况下，将客户端应用的 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 设置为 `builder.HostEnvironment.BaseAddress` 的 URI。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-200">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="3e5c2-201">在从 Blazor 托管的项目模板创建的托管应用的默认配置中，不需要 CORS 配置。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-201">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="3e5c2-202">不由服务器项目托管，并且不共享服务器应用的基址的其他客户端应用在服务器项目中需要 CORS 配置。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-202">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="3e5c2-203">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试程序组件 (`Components/HTTPRequestTester.razor`)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-203">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="3e5c2-204">处理令牌请求错误</span><span class="sxs-lookup"><span data-stu-id="3e5c2-204">Handle token request errors</span></span>

<span data-ttu-id="3e5c2-205">当单页应用程序 (SPA) 使用 Open ID Connect (OIDC) 对用户进行身份验证时，身份验证状态将以会话 cookie（因用户提供其凭据而设置）的形式在 SPA 内和 Identity 提供者 (IP) 中进行本地维护。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-205">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="3e5c2-206">IP 为用户发出的令牌通常在短时间（约 1 小时）内有效，因此客户端应用必须定期提取新令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-206">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="3e5c2-207">否则，在授予的令牌到期后，将注销用户。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-207">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="3e5c2-208">在大多数情况下，由于身份验证状态或“会话”保留在 IP 中，因此 OIDC 客户端能够预配新令牌，而无需用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-208">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="3e5c2-209">在某些情况下，如果没有用户交互，客户端就无法获得令牌（例如，当用户出于某种原因而明确从 IP 注销时）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-209">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="3e5c2-210">如果用户访问 `https://login.microsoftonline.com` 并注销，则会发生这种情况。在这些场景下，应用不会立即知道用户已注销。客户端持有的任何令牌都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-210">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="3e5c2-211">另外，当前令牌过期后，如果没有用户交互，客户端将无法预配新令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-211">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="3e5c2-212">这些场景并不特定于基于令牌的身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-212">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="3e5c2-213">它们本就是 SPA 的一部分。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-213">They are part of the nature of SPAs.</span></span> <span data-ttu-id="3e5c2-214">如果删除了身份验证cookie，则使用 cookie 的 SPA 也无法调用服务器 API。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-214">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="3e5c2-215">当应用对受保护的资源执行 API 调用时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-215">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="3e5c2-216">要预配新的访问令牌来调用 API，可能需要用户再次进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-216">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="3e5c2-217">即使客户端拥有看似有效的令牌，对服务器的调用也可能会失败，因为该令牌已由用户撤销。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-217">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="3e5c2-218">当应用请求令牌时，有两种可能的结果：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-218">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="3e5c2-219">请求成功，应用拥有有效的令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-219">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="3e5c2-220">请求失败，应用必须再次验证用户身份才能获得新令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-220">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="3e5c2-221">当令牌请求失败时，需要决定在执行重定向之前是否要保存任何当前状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-221">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="3e5c2-222">随着复杂程度的提高，可以使用以下几种方法：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-222">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="3e5c2-223">将当前页面状态存储在会话存储中。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-223">Store the current page state in session storage.</span></span> <span data-ttu-id="3e5c2-224">在 [`OnInitializedAsync` 生命周期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期间，检查状态是否可以还原，然后再继续执行操作。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-224">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="3e5c2-225">添加查询字符串参数，并使用该参数向应用发送信号，而该应用需要重新水化先前保存的状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-225">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="3e5c2-226">添加具有唯一标识符的查询字符串参数以将数据存储在会话存储中，而不会导致与其他项发生冲突。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-226">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="3e5c2-227">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-227">The following example shows how to:</span></span>

* <span data-ttu-id="3e5c2-228">在重定向到登录页面之前保留状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-228">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="3e5c2-229">使用查询字符串参数恢复先前经过身份验证后的状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-229">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="3e5c2-230">在身份验证操作之前保存应用状态</span><span class="sxs-lookup"><span data-stu-id="3e5c2-230">Save app state before an authentication operation</span></span>

<span data-ttu-id="3e5c2-231">在身份验证操作过程中，有时要在将浏览器重定向到 IP 之前保存应用状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-231">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="3e5c2-232">其中一种情况是使用状态容器并希望在身份验证成功后恢复状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-232">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="3e5c2-233">可以使用自定义身份验证状态对象来保留特定于应用的状态或对其的引用，并在身份验证操作成功完成期间恢复该状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-233">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="3e5c2-234">下面的示例演示了该方法。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-234">The following example demonstrates the approach.</span></span>

<span data-ttu-id="3e5c2-235">在应用中创建状态容器类，该类具有用于保存应用的状态值的属性。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-235">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="3e5c2-236">在以下示例中，容器用于维护默认项目模板的 `Counter` 组件 (`Pages/Counter.razor`) 的计数器值。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-236">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="3e5c2-237">用于序列化和反序列化容器的方法基于 <xref:System.Text.Json>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-237">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="3e5c2-238">`Counter` 组件使用状态容器来维护组件以外的 `currentCount` 值：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-238">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="3e5c2-239">从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 创建 `ApplicationAuthenticationState`。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-239">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="3e5c2-240">提供 `Id` 属性，该属性用作本地存储状态的标识符。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-240">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="3e5c2-241">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-241">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="3e5c2-242">`Authentication` 组件 (`Pages/Authentication.razor`) 使用本地会话存储以及 `StateContainer` 序列化和反序列化方法（`GetStateForLocalStorage` 和 `SetStateFromLocalStorage`）保存和还原应用的状态：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-242">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="3e5c2-243">本示例使用 Azure Active Directory (AAD) 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-243">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="3e5c2-244">在 `Program.Main` (`Program.cs`) 中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-244">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="3e5c2-245">将 `ApplicationAuthenticationState` 配置为 Microsoft 身份验证库 (MSAL)`RemoteAuthenticationState` 类型。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-245">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="3e5c2-246">在服务容器中注册状态容器。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-246">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="3e5c2-247">自定义应用路由</span><span class="sxs-lookup"><span data-stu-id="3e5c2-247">Customize app routes</span></span>

<span data-ttu-id="3e5c2-248">默认情况下，[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 库使用下表中显示的路由表示不同的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-248">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="3e5c2-249">路由</span><span class="sxs-lookup"><span data-stu-id="3e5c2-249">Route</span></span>                            | <span data-ttu-id="3e5c2-250">目标</span><span class="sxs-lookup"><span data-stu-id="3e5c2-250">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="3e5c2-251">触发登录操作。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-251">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="3e5c2-252">处理任何登录操作的结果。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-252">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="3e5c2-253">当登录操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-253">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="3e5c2-254">触发注销操作。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-254">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="3e5c2-255">处理注销操作的结果。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-255">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="3e5c2-256">当注销操作由于某种原因失败时显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-256">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="3e5c2-257">指示用户已成功注销。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-257">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="3e5c2-258">触发操作以编辑用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-258">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="3e5c2-259">触发操作以注册新用户。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-259">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="3e5c2-260">上表中显示的路由可通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-260">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3e5c2-261">设置选项以提供自定义路由时，确认应用具有可处理每个路径的路由。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-261">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="3e5c2-262">在以下示例中，所有路径都带有 `/security` 前缀。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-262">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="3e5c2-263">`Authentication` 组件 (`Pages/Authentication.razor`)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-263">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3e5c2-264">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3e5c2-264">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="3e5c2-265">如果需求要求使用完全不同的路径，请按照前面所述设置路由，并使用显式的操作参数呈现 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-265">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="3e5c2-266">如果选择这样做，则允许将 UI 分成不同的页面。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-266">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="3e5c2-267">自定义身份验证用户界面</span><span class="sxs-lookup"><span data-stu-id="3e5c2-267">Customize the authentication user interface</span></span>

<span data-ttu-id="3e5c2-268"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 包括每个身份验证状态的一组默认 UI 片段。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-268"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="3e5c2-269">可以通过传入自定义 <xref:Microsoft.AspNetCore.Components.RenderFragment> 自定义每个状态。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-269">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="3e5c2-270">要在初始登录过程中自定义显示的文本，可以按如下所示更改 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-270">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="3e5c2-271">`Authentication` 组件 (`Pages/Authentication.razor`)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-271">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="3e5c2-272">下表中显示了 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 的一个片段，该片段可用于每个身份验证路由。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-272">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="3e5c2-273">路由</span><span class="sxs-lookup"><span data-stu-id="3e5c2-273">Route</span></span>                            | <span data-ttu-id="3e5c2-274">Fragment</span><span class="sxs-lookup"><span data-stu-id="3e5c2-274">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="3e5c2-275">自定义用户</span><span class="sxs-lookup"><span data-stu-id="3e5c2-275">Customize the user</span></span>

<span data-ttu-id="3e5c2-276">可自定义绑定到应用的用户。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-276">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="3e5c2-277">使用有效负载声明自定义用户</span><span class="sxs-lookup"><span data-stu-id="3e5c2-277">Customize the user with a payload claim</span></span>

<span data-ttu-id="3e5c2-278">在以下示例中，所有经过应用身份验证的用户都会收到每种用户身份验证方法的 `amr` 声明。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-278">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="3e5c2-279">`amr` 声明确定在 Microsoft Identity Platform v1.0 [有效负载声明](/azure/active-directory/develop/access-tokens#the-amr-claim)中如何对令牌主体进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-279">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="3e5c2-280">该示例使用基于 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-280">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="3e5c2-281">创建扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 类的类。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-281">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="3e5c2-282">下面的示例将 `AuthenticationMethod` 属性设置为用户的 `amr` JSON 属性值数组。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-282">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="3e5c2-283">对用户进行身份验证时，由框架自动填充 `AuthenticationMethod`。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-283">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="3e5c2-284">创建一个扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 的工厂，根据存储在 `CustomUserAccount.AuthenticationMethod` 中的用户身份验证方法创建声明：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-284">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

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

<span data-ttu-id="3e5c2-285">为正在使用的验证提供程序注册 `CustomAccountFactory`。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-285">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="3e5c2-286">以下任何注册均有效：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-286">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="3e5c2-287"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-287"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="3e5c2-288"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-288"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="3e5c2-289"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-289"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

### <a name="customize-the-user-with-graph-api-claims"></a><span data-ttu-id="3e5c2-290">使用图形 API 声明自定义用户</span><span class="sxs-lookup"><span data-stu-id="3e5c2-290">Customize the user with Graph API claims</span></span>

<span data-ttu-id="3e5c2-291">在下面的示例中，应用使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 根据图形 API 为用户创建移动电话号码声明。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-291">In the following example, the app creates a mobile phone number claim for the user from Graph API using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span> <span data-ttu-id="3e5c2-292">应用必须在 AAD 中配置 `User.Read` 图形 API 权限（作用域）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-292">The app must have the `User.Read` Graph API permission (scope) configured in AAD.</span></span>

<span data-ttu-id="3e5c2-293">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-293">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="3e5c2-294">图形 API 的命名 <xref:System.Net.Http.HttpClient> 是在 `Program.Main` (`Program.cs`) 中使用 `GraphAPIAuthorizationMessageHandler` 创建的：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-294">A named <xref:System.Net.Http.HttpClient> for Graph API is created in `Program.Main` (`Program.cs`) using the `GraphAPIAuthorizationMessageHandler`:</span></span>

```csharp
using System;

...

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="3e5c2-295">`Models/UserInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="3e5c2-295">`Models/UserInfo.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="3e5c2-296">在下面的 `CustomAccountFactory` (`CustomAccountFactory.cs`) 中，框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 表示用户的帐户。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-296">In the following `CustomAccountFactory` (`CustomAccountFactory.cs`), the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="3e5c2-297">如果应用需要扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请在以下代码中将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-297">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {MESSAGE}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="3e5c2-298">在 `Program.Main` (`Program.cs`) 中，将应用配置为使用自定义工厂。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-298">In `Program.Main` (`Program.cs`), configure the app to use the custom factory.</span></span> <span data-ttu-id="3e5c2-299">如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-299">If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="3e5c2-300">前面的示例适用于使用 AAD 身份验证和 MSAL 的应用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-300">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="3e5c2-301">对于 OIDC 和 API 身份验证，也存在类似的模式。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-301">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="3e5c2-302">有关详细信息，请参阅[使用有效负载声明自定义用户](#customize-the-user-with-a-payload-claim)部分结尾处的示例。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-302">For more information, see the examples at the end of the [Customize the user with a payload claim](#customize-the-user-with-a-payload-claim) section.</span></span>

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="3e5c2-303">具有自定义用户帐户类的 AAD 安全组和角色</span><span class="sxs-lookup"><span data-stu-id="3e5c2-303">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="3e5c2-304">关于使用 AAD 安全组、AAD 管理员角色和自定义用户帐户类的其他示例，请参阅 <xref:blazor/security/webassembly/aad-groups-roles>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-304">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="3e5c2-305">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="3e5c2-305">Support prerendering with authentication</span></span>

<span data-ttu-id="3e5c2-306">遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-306">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="3e5c2-307">预呈现不需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-307">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="3e5c2-308">不预呈现需要授权的路径。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-308">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="3e5c2-309">在 `Client` 应用的 `Program` 类 (`Program.cs`) 中，将常见服务注册纳入单独的方法（例如 `ConfigureCommonServices`）：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-309">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

<span data-ttu-id="3e5c2-310">在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-310">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="3e5c2-311">在服务器应用的 `Startup.Configure` 方法中，将 [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 替换为 [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-311">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="3e5c2-312">在服务器应用中，如果不存在 `Pages` 文件夹，请创建一个。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-312">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="3e5c2-313">在服务器应用的 `Pages` 文件夹中创建 `_Host.cshtml` 页。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-313">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="3e5c2-314">将 `Client` 应用 `wwwroot/index.html` 文件中的内容粘贴到 `Pages/_Host.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-314">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="3e5c2-315">更新文件的内容：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-315">Update the file's contents:</span></span>

* <span data-ttu-id="3e5c2-316">将 `@page "_Host"` 添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-316">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="3e5c2-317">将 `<app>Loading...</app>` 标记替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-317">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="3e5c2-318">用于托管应用和第三方登录提供程序的选项</span><span class="sxs-lookup"><span data-stu-id="3e5c2-318">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="3e5c2-319">使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-319">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="3e5c2-320">选择哪一种选项取决于方案。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-320">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="3e5c2-321">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-321">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="3e5c2-322">对用户进行身份验证，以仅调用受保护的第三方 API</span><span class="sxs-lookup"><span data-stu-id="3e5c2-322">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="3e5c2-323">使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-323">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="3e5c2-324">在本方案中：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-324">In this scenario:</span></span>

* <span data-ttu-id="3e5c2-325">托管应用的服务器不会发挥作用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-325">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="3e5c2-326">无法保护服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-326">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="3e5c2-327">应用只能调用受保护的第三方 API。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-327">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="3e5c2-328">使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API</span><span class="sxs-lookup"><span data-stu-id="3e5c2-328">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="3e5c2-329">使用第三方登录提供程序配置 Identity。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-329">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="3e5c2-330">获取第三方 API 访问所需的令牌并进行存储。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-330">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="3e5c2-331">当用户登录时，Identity 将在身份验证过程中收集访问令牌和刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-331">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="3e5c2-332">此时，可通过几种方法向第三方 API 进行 API 调用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-332">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="3e5c2-333">使用服务器访问令牌检索第三方访问令牌</span><span class="sxs-lookup"><span data-stu-id="3e5c2-333">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="3e5c2-334">使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-334">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="3e5c2-335">在此处，使用第三方访问令牌直接从客户端上的 Identity 调用第三方 API 资源。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-335">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="3e5c2-336">我们不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-336">We don't recommend this approach.</span></span> <span data-ttu-id="3e5c2-337">此方法需要将第三方访问令牌视为针对公共客户端生成。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-337">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="3e5c2-338">在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-338">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="3e5c2-339">机密客户端具有客户端机密，并且假定能够安全地存储机密。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-339">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="3e5c2-340">第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-340">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="3e5c2-341">同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-341">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="3e5c2-342">从客户端向服务器 API 发出 API 调用以便调用第三方 API</span><span class="sxs-lookup"><span data-stu-id="3e5c2-342">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="3e5c2-343">从客户端向服务器 API 发出 API 调用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-343">Make an API call from the client to the server API.</span></span> <span data-ttu-id="3e5c2-344">从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-344">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="3e5c2-345">尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-345">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="3e5c2-346">服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-346">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="3e5c2-347">应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-347">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="3e5c2-348">使用 OpenID Connect (OIDC) v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="3e5c2-348">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="3e5c2-349">身份验证库和 Blazor 项目模板使用 Open ID Connect (OIDC) v1.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-349">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="3e5c2-350">要使用 v2.0 终结点，请配置 JWT 持有者 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 选项。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-350">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="3e5c2-351">在下面的示例中，通过向 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> 属性追加 `v2.0` 段，将 AAD 配置为 v2.0：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-351">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="3e5c2-352">也可以在应用设置 (`appsettings.json`) 文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-352">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="3e5c2-353">如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-353">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="3e5c2-354">使用 `Authority` 键在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 或应用设置文件 (`appsettings.json`) 中设置属性。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-354">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="3e5c2-355">ID 令牌中的声明列表针对 v2.0 终结点会发生更改。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-355">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="3e5c2-356">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-356">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="3e5c2-357">在组件中配置和使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="3e5c2-357">Configure and use gRPC in components</span></span>

<span data-ttu-id="3e5c2-358">若要将 Blazor WebAssembly 应用配置为使用 [ASP.NET Core gRPC 框架](xref:grpc/index)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-358">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="3e5c2-359">在服务器上启用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-359">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="3e5c2-360">有关详细信息，请参阅 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-360">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="3e5c2-361">为应用的消息处理程序注册 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-361">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="3e5c2-362">下面的示例将应用的授权消息处理程序配置为使用 [gRPC 教程中的 `GreeterClient` 服务](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`)：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-362">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="3e5c2-363">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-363">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="3e5c2-364">将 `.proto` 文件放在托管的 Blazor 解决方案的 `Shared` 项目中。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-364">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="3e5c2-365">客户端应用中的组件可使用 gRPC 客户端 (`Pages/Grpc.razor`) 发出 gRPC 调用：</span><span class="sxs-lookup"><span data-stu-id="3e5c2-365">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="3e5c2-366">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-366">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="3e5c2-367">若要使用 `Status.DebugException` 属性，请使用 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 版本 2.30.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-367">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="3e5c2-368">有关详细信息，请参阅 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="3e5c2-368">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e5c2-369">其他资源</span><span class="sxs-lookup"><span data-stu-id="3e5c2-369">Additional resources</span></span>

* [<span data-ttu-id="3e5c2-370">具有提取 API 请求选项的 `HttpClient` 和 `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="3e5c2-370">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
