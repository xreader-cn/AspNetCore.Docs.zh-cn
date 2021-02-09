---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
no-loc:
- appsettings.json
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057325"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="6182e-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="6182e-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6182e-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Steve Gordon](https://github.com/stevejgordon)、[Glenn Condron](https://github.com/glennc) 和 [Ryan Nowak](https://github.com/rynowak)。</span><span class="sxs-lookup"><span data-stu-id="6182e-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="6182e-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6182e-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="6182e-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="6182e-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6182e-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="6182e-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6182e-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="6182e-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="6182e-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="6182e-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="6182e-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="6182e-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="6182e-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="6182e-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6182e-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6182e-115">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="6182e-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6182e-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="6182e-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="6182e-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="6182e-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6182e-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="6182e-118">Consumption patterns</span></span>

<span data-ttu-id="6182e-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="6182e-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6182e-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6182e-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6182e-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6182e-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6182e-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="6182e-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6182e-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-125">Basic usage</span></span>

<span data-ttu-id="6182e-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="6182e-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="6182e-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="6182e-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6182e-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="6182e-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6182e-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="6182e-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="6182e-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="6182e-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="6182e-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="6182e-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6182e-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-132">Named clients</span></span>

<span data-ttu-id="6182e-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="6182e-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="6182e-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="6182e-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="6182e-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="6182e-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="6182e-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="6182e-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6182e-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="6182e-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="6182e-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="6182e-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="6182e-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="6182e-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="6182e-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="6182e-140">CreateClient</span></span>

<span data-ttu-id="6182e-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="6182e-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="6182e-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="6182e-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="6182e-143">The configuration action is called.</span></span>

<span data-ttu-id="6182e-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="6182e-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6182e-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="6182e-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6182e-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="6182e-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6182e-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-147">Typed clients</span></span>

<span data-ttu-id="6182e-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-148">Typed clients:</span></span>

* <span data-ttu-id="6182e-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="6182e-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6182e-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="6182e-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6182e-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="6182e-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6182e-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="6182e-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="6182e-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="6182e-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="6182e-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="6182e-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="6182e-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="6182e-156">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="6182e-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="6182e-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="6182e-157">In the preceding code:</span></span>

* <span data-ttu-id="6182e-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="6182e-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="6182e-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="6182e-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="6182e-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="6182e-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6182e-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="6182e-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="6182e-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6182e-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6182e-164">在上述代码中，`AddHttpClient` 将 `GitHubService` 注册为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="6182e-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="6182e-165">此注册使用工厂方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6182e-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="6182e-166">创建 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="6182e-167">创建 `GitHubService` 的实例，将 `HttpClient` 的实例传入其构造函数。</span><span class="sxs-lookup"><span data-stu-id="6182e-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="6182e-168">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6182e-169">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="6182e-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6182e-170">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="6182e-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="6182e-171">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="6182e-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6182e-172">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="6182e-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="6182e-173">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6182e-174">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-174">Generated clients</span></span>

<span data-ttu-id="6182e-175">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="6182e-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6182e-176">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="6182e-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6182e-177">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="6182e-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6182e-178">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="6182e-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6182e-179">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="6182e-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6182e-180">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="6182e-181">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="6182e-182">发出 POST、PUT 和 DELETE 请求</span><span class="sxs-lookup"><span data-stu-id="6182e-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="6182e-183">在前面的示例中，所有 HTTP 请求均使用 GET HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="6182e-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="6182e-184">`HttpClient` 还支持其他 HTTP 谓词，其中包括：</span><span class="sxs-lookup"><span data-stu-id="6182e-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="6182e-185">POST</span><span class="sxs-lookup"><span data-stu-id="6182e-185">POST</span></span>
* <span data-ttu-id="6182e-186">PUT</span><span class="sxs-lookup"><span data-stu-id="6182e-186">PUT</span></span>
* <span data-ttu-id="6182e-187">删除</span><span class="sxs-lookup"><span data-stu-id="6182e-187">DELETE</span></span>
* <span data-ttu-id="6182e-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="6182e-188">PATCH</span></span>

<span data-ttu-id="6182e-189">有关受支持的 HTTP 谓词的完整列表，请参阅 <xref:System.Net.Http.HttpMethod>。</span><span class="sxs-lookup"><span data-stu-id="6182e-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="6182e-190">下面的示例演示如何发出 HTTP POST 请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="6182e-191">在前面的代码中，`CreateItemAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="6182e-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="6182e-192">使用 `System.Text.Json` 将 `TodoItem` 参数序列化为 JSON。</span><span class="sxs-lookup"><span data-stu-id="6182e-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="6182e-193">这将使用 <xref:System.Text.Json.JsonSerializerOptions> 的实例来配置序列化过程。</span><span class="sxs-lookup"><span data-stu-id="6182e-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="6182e-194">创建 <xref:System.Net.Http.StringContent> 的实例，以打包序列化的 JSON 以便在 HTTP 请求的正文中发送。</span><span class="sxs-lookup"><span data-stu-id="6182e-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="6182e-195">调用 <xref:System.Net.Http.HttpClient.PostAsync%2A> 将 JSON 内容发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="6182e-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="6182e-196">这是添加到 [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) 的相对 URL。</span><span class="sxs-lookup"><span data-stu-id="6182e-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="6182e-197">如果响应状态代码不指示成功，则调用 <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> 引发异常。</span><span class="sxs-lookup"><span data-stu-id="6182e-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="6182e-198">`HttpClient` 还支持其他类型的内容。</span><span class="sxs-lookup"><span data-stu-id="6182e-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="6182e-199">例如，<xref:System.Net.Http.MultipartContent> 和 <xref:System.Net.Http.StreamContent>。</span><span class="sxs-lookup"><span data-stu-id="6182e-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="6182e-200">有关受支持的内容的完整列表，请参阅 <xref:System.Net.Http.HttpContent>。</span><span class="sxs-lookup"><span data-stu-id="6182e-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="6182e-201">下面的示例演示了一个 HTTP PUT 请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="6182e-202">前面的代码与 POST 示例非常相似。</span><span class="sxs-lookup"><span data-stu-id="6182e-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="6182e-203">`SaveItemAsync` 方法调用 <xref:System.Net.Http.HttpClient.PutAsync%2A> 而不是 `PostAsync`。</span><span class="sxs-lookup"><span data-stu-id="6182e-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="6182e-204">下面的示例演示了一个 HTTP DELETE 请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="6182e-205">在前面的代码中，`DeleteItemAsync` 方法调用 <xref:System.Net.Http.HttpClient.DeleteAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="6182e-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="6182e-206">由于 HTTP DELETE 请求通常不包含正文，因此 `DeleteAsync` 方法不提供接受 `HttpContent` 实例的重载。</span><span class="sxs-lookup"><span data-stu-id="6182e-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="6182e-207">要详细了解如何将不同的 HTTP 谓词用于 `HttpClient`，请参阅 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="6182e-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6182e-208">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="6182e-208">Outgoing request middleware</span></span>

<span data-ttu-id="6182e-209">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6182e-210">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="6182e-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="6182e-211">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="6182e-212">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="6182e-213">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="6182e-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="6182e-214">此模式：</span><span class="sxs-lookup"><span data-stu-id="6182e-214">This pattern:</span></span>
  
    * <span data-ttu-id="6182e-215">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="6182e-216">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="6182e-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="6182e-217">缓存</span><span class="sxs-lookup"><span data-stu-id="6182e-217">caching</span></span>
      * <span data-ttu-id="6182e-218">错误处理</span><span class="sxs-lookup"><span data-stu-id="6182e-218">error handling</span></span>
      * <span data-ttu-id="6182e-219">序列化</span><span class="sxs-lookup"><span data-stu-id="6182e-219">serialization</span></span>
      * <span data-ttu-id="6182e-220">日志记录</span><span class="sxs-lookup"><span data-stu-id="6182e-220">logging</span></span>

<span data-ttu-id="6182e-221">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="6182e-221">To create a delegating handler:</span></span>

* <span data-ttu-id="6182e-222">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="6182e-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="6182e-223">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="6182e-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="6182e-224">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="6182e-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6182e-225">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="6182e-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="6182e-226">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="6182e-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="6182e-227">可使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="6182e-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="6182e-228">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="6182e-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6182e-229">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="6182e-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6182e-230">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6182e-231">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="6182e-232">在出站请求中间件中使用 DI</span><span class="sxs-lookup"><span data-stu-id="6182e-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="6182e-233">当 `IHttpClientFactory` 创建新的委托处理程序时，它使用 DI 来完成处理程序的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="6182e-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="6182e-234">`IHttpClientFactory` 为每个处理程序创建单独的 DI 范围，当处理程序使用限定范围的服务时，这可能导致意外的行为。</span><span class="sxs-lookup"><span data-stu-id="6182e-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="6182e-235">例如，请考虑下面的接口及其实现，它将任务表示为带有标识符 `OperationId` 的操作：</span><span class="sxs-lookup"><span data-stu-id="6182e-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="6182e-236">顾名思义，使用限定范围的生存期向 DI 注册 `IOperationScoped`：</span><span class="sxs-lookup"><span data-stu-id="6182e-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="6182e-237">以下委托处理程序消耗并使用 `IOperationScoped` 来设置传出请求的 `X-OPERATION-ID` 标头：</span><span class="sxs-lookup"><span data-stu-id="6182e-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="6182e-238">在[`HttpRequestsSample`下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] 中，导航到 `/Operation` 并刷新页面。</span><span class="sxs-lookup"><span data-stu-id="6182e-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="6182e-239">每个请求的请求范围值发生更改，但处理程序范围值仅每 5 秒钟更改一次。</span><span class="sxs-lookup"><span data-stu-id="6182e-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="6182e-240">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="6182e-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="6182e-241">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="6182e-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6182e-242">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="6182e-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6182e-243">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="6182e-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="6182e-244">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="6182e-245">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="6182e-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6182e-246">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-246">Use Polly-based handlers</span></span>

<span data-ttu-id="6182e-247">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="6182e-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6182e-248">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="6182e-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6182e-249">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="6182e-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6182e-250">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6182e-251">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="6182e-252">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6182e-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6182e-253">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="6182e-253">Handle transient faults</span></span>

<span data-ttu-id="6182e-254">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6182e-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="6182e-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6182e-256">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="6182e-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="6182e-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="6182e-257">HTTP 5xx</span></span>
* <span data-ttu-id="6182e-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="6182e-258">HTTP 408</span></span>

<span data-ttu-id="6182e-259">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="6182e-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="6182e-260">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6182e-261">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="6182e-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6182e-262">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="6182e-262">Dynamically select policies</span></span>

<span data-ttu-id="6182e-263">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="6182e-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="6182e-264">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="6182e-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6182e-265">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6182e-266">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6182e-267">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="6182e-268">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="6182e-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6182e-269">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="6182e-269">In the preceding example:</span></span>

* <span data-ttu-id="6182e-270">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-270">Two handlers are added.</span></span>
* <span data-ttu-id="6182e-271">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="6182e-272">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="6182e-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="6182e-273">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="6182e-274">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="6182e-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="6182e-275">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6182e-276">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6182e-277">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="6182e-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="6182e-278">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="6182e-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="6182e-279">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="6182e-279">In the following code:</span></span>

* <span data-ttu-id="6182e-280">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="6182e-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="6182e-282">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="6182e-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6182e-283">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="6182e-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="6182e-284">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-285">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="6182e-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="6182e-286">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="6182e-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6182e-287">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="6182e-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6182e-288">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="6182e-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6182e-289">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6182e-290">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="6182e-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6182e-291">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="6182e-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="6182e-292">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="6182e-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6182e-293">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="6182e-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="6182e-294">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="6182e-295">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6182e-296">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="6182e-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="6182e-297">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-298">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6182e-299">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="6182e-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6182e-300">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="6182e-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6182e-301">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6182e-302">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6182e-303">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6182e-304">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="6182e-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6182e-305">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6182e-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6182e-306">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6182e-307">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6182e-308">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="6182e-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6182e-309">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="6182e-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6182e-310">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6182e-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="6182e-311">Cookies</span></span>

<span data-ttu-id="6182e-312">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6182e-313">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="6182e-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6182e-314">对于需要 cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6182e-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6182e-315">禁用自动 cookie 处理</span><span class="sxs-lookup"><span data-stu-id="6182e-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6182e-316">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6182e-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6182e-317">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="6182e-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6182e-318">Logging</span><span class="sxs-lookup"><span data-stu-id="6182e-318">Logging</span></span>

<span data-ttu-id="6182e-319">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6182e-320">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="6182e-321">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="6182e-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6182e-322">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="6182e-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6182e-323">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="6182e-324">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6182e-325">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6182e-326">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6182e-327">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6182e-328">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”。</span><span class="sxs-lookup"><span data-stu-id="6182e-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="6182e-329">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="6182e-330">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6182e-331">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6182e-332">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="6182e-333">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="6182e-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6182e-334">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6182e-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6182e-335">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="6182e-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6182e-336">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="6182e-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6182e-337"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="6182e-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6182e-338">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="6182e-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6182e-339">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6182e-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6182e-340">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="6182e-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6182e-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6182e-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6182e-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6182e-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6182e-343">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="6182e-343">In the following example:</span></span>

* <span data-ttu-id="6182e-344"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="6182e-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6182e-345">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6182e-346">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="6182e-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6182e-347">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="6182e-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="6182e-348">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="6182e-348">Header propagation middleware</span></span>

<span data-ttu-id="6182e-349">标头传播是一个 ASP.NET Core 中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="6182e-350">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="6182e-350">To use header propagation:</span></span>

* <span data-ttu-id="6182e-351">引用 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 包。</span><span class="sxs-lookup"><span data-stu-id="6182e-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="6182e-352">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="6182e-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="6182e-353">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="6182e-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="6182e-354">其他资源</span><span class="sxs-lookup"><span data-stu-id="6182e-354">Additional resources</span></span>

* [<span data-ttu-id="6182e-355">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="6182e-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6182e-356">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="6182e-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6182e-357">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="6182e-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="6182e-358">如何在 .NET 中对 JSON 数据进行序列化和反序列化</span><span class="sxs-lookup"><span data-stu-id="6182e-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6182e-359">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6182e-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6182e-360">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6182e-361">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="6182e-361">It offers the following benefits:</span></span>

* <span data-ttu-id="6182e-362">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6182e-363">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="6182e-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6182e-364">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="6182e-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6182e-365">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="6182e-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6182e-366">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6182e-367">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6182e-368">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6182e-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6182e-369">消耗模式</span><span class="sxs-lookup"><span data-stu-id="6182e-369">Consumption patterns</span></span>

<span data-ttu-id="6182e-370">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="6182e-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6182e-371">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6182e-372">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6182e-373">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6182e-374">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6182e-375">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="6182e-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="6182e-376">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="6182e-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6182e-377">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-377">Basic usage</span></span>

<span data-ttu-id="6182e-378">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="6182e-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6182e-379">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="6182e-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6182e-380">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="6182e-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6182e-381">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="6182e-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6182e-382">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="6182e-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6182e-383">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="6182e-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6182e-384">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-384">Named clients</span></span>

<span data-ttu-id="6182e-385">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6182e-386">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="6182e-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6182e-387">上面的代码调用 `AddHttpClient`，同时提供名称“github”。</span><span class="sxs-lookup"><span data-stu-id="6182e-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6182e-388">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="6182e-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6182e-389">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="6182e-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6182e-390">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6182e-391">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="6182e-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6182e-392">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="6182e-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6182e-393">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="6182e-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6182e-394">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-394">Typed clients</span></span>

<span data-ttu-id="6182e-395">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-395">Typed clients:</span></span>

* <span data-ttu-id="6182e-396">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="6182e-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6182e-397">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="6182e-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6182e-398">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="6182e-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6182e-399">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="6182e-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6182e-400">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="6182e-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6182e-401">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="6182e-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6182e-402">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="6182e-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6182e-403">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="6182e-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6182e-404">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="6182e-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6182e-405">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="6182e-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6182e-406">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="6182e-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6182e-407">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6182e-408">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6182e-409">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="6182e-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6182e-410">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="6182e-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6182e-411">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6182e-412">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="6182e-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6182e-413">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="6182e-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6182e-414">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-414">Generated clients</span></span>

<span data-ttu-id="6182e-415">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="6182e-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6182e-416">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="6182e-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6182e-417">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="6182e-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6182e-418">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="6182e-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6182e-419">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="6182e-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6182e-420">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="6182e-421">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6182e-422">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="6182e-422">Outgoing request middleware</span></span>

<span data-ttu-id="6182e-423">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6182e-424">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6182e-425">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6182e-426">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="6182e-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6182e-427">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6182e-428">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="6182e-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6182e-429">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="6182e-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6182e-430">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="6182e-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6182e-431">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6182e-432">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="6182e-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6182e-433">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="6182e-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6182e-434">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="6182e-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6182e-435">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="6182e-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6182e-436">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="6182e-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6182e-437">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="6182e-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="6182e-438">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="6182e-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="6182e-439">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="6182e-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6182e-440">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="6182e-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6182e-441">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6182e-442">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6182e-443">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="6182e-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6182e-444">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6182e-445">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6182e-446">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="6182e-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6182e-447">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-447">Use Polly-based handlers</span></span>

<span data-ttu-id="6182e-448">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="6182e-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6182e-449">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="6182e-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6182e-450">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="6182e-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6182e-451">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6182e-452">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="6182e-452">The Polly extensions:</span></span>

* <span data-ttu-id="6182e-453">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6182e-454">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="6182e-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6182e-455">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="6182e-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6182e-456">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="6182e-456">Handle transient faults</span></span>

<span data-ttu-id="6182e-457">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6182e-458">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="6182e-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6182e-459">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="6182e-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6182e-460">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="6182e-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6182e-461">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="6182e-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6182e-462">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6182e-463">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="6182e-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6182e-464">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="6182e-464">Dynamically select policies</span></span>

<span data-ttu-id="6182e-465">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6182e-466">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="6182e-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6182e-467">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6182e-468">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6182e-469">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6182e-470">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="6182e-471">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="6182e-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6182e-472">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6182e-473">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6182e-474">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="6182e-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6182e-475">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6182e-476">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="6182e-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6182e-477">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6182e-478">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6182e-479">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="6182e-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="6182e-480">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="6182e-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6182e-481">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="6182e-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6182e-482">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="6182e-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6182e-483">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="6182e-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6182e-484">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="6182e-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6182e-485">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="6182e-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="6182e-486">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-487">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="6182e-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6182e-488">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="6182e-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6182e-489">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="6182e-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6182e-490">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="6182e-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6182e-491">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6182e-492">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="6182e-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6182e-493">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="6182e-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6182e-494">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="6182e-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6182e-495">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="6182e-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6182e-496">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="6182e-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6182e-497">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="6182e-498">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6182e-499">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="6182e-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6182e-500">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6182e-501">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-502">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6182e-503">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="6182e-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6182e-504">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="6182e-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6182e-505">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6182e-506">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6182e-507">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6182e-508">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="6182e-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6182e-509">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6182e-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6182e-510">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6182e-511">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6182e-512">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="6182e-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6182e-513">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="6182e-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6182e-514">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6182e-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="6182e-515">Cookies</span></span>

<span data-ttu-id="6182e-516">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6182e-517">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="6182e-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6182e-518">对于需要 cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6182e-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6182e-519">禁用自动 cookie 处理</span><span class="sxs-lookup"><span data-stu-id="6182e-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6182e-520">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6182e-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6182e-521">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="6182e-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6182e-522">Logging</span><span class="sxs-lookup"><span data-stu-id="6182e-522">Logging</span></span>

<span data-ttu-id="6182e-523">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6182e-524">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6182e-525">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="6182e-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6182e-526">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="6182e-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6182e-527">例如，名为“MyNamedClient”的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6182e-528">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6182e-529">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6182e-530">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6182e-531">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6182e-532">在“MyNamedClient”示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="6182e-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6182e-533">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6182e-534">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6182e-535">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6182e-536">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6182e-537">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="6182e-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6182e-538">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6182e-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6182e-539">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="6182e-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6182e-540">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="6182e-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6182e-541"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="6182e-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6182e-542">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="6182e-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6182e-543">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6182e-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6182e-544">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="6182e-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6182e-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6182e-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6182e-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6182e-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6182e-547">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="6182e-547">In the following example:</span></span>

* <span data-ttu-id="6182e-548"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="6182e-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6182e-549">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6182e-550">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="6182e-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6182e-551">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="6182e-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="6182e-552">其他资源</span><span class="sxs-lookup"><span data-stu-id="6182e-552">Additional resources</span></span>

* [<span data-ttu-id="6182e-553">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="6182e-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6182e-554">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="6182e-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6182e-555">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="6182e-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6182e-556">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6182e-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6182e-557">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6182e-558">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="6182e-558">It offers the following benefits:</span></span>

* <span data-ttu-id="6182e-559">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6182e-560">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="6182e-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6182e-561">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="6182e-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6182e-562">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="6182e-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6182e-563">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6182e-564">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6182e-565">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6182e-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6182e-566">先决条件</span><span class="sxs-lookup"><span data-stu-id="6182e-566">Prerequisites</span></span>

<span data-ttu-id="6182e-567">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6182e-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="6182e-568">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="6182e-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6182e-569">消耗模式</span><span class="sxs-lookup"><span data-stu-id="6182e-569">Consumption patterns</span></span>

<span data-ttu-id="6182e-570">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="6182e-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6182e-571">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6182e-572">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6182e-573">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6182e-574">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6182e-575">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="6182e-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="6182e-576">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="6182e-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6182e-577">基本用法</span><span class="sxs-lookup"><span data-stu-id="6182e-577">Basic usage</span></span>

<span data-ttu-id="6182e-578">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="6182e-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6182e-579">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="6182e-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6182e-580">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="6182e-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6182e-581">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="6182e-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6182e-582">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="6182e-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6182e-583">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="6182e-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6182e-584">命名客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-584">Named clients</span></span>

<span data-ttu-id="6182e-585">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6182e-586">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="6182e-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6182e-587">上面的代码调用 `AddHttpClient`，同时提供名称“github”。</span><span class="sxs-lookup"><span data-stu-id="6182e-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6182e-588">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="6182e-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6182e-589">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="6182e-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6182e-590">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6182e-591">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="6182e-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6182e-592">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="6182e-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6182e-593">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="6182e-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6182e-594">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-594">Typed clients</span></span>

<span data-ttu-id="6182e-595">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-595">Typed clients:</span></span>

* <span data-ttu-id="6182e-596">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="6182e-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6182e-597">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="6182e-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6182e-598">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="6182e-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6182e-599">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="6182e-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6182e-600">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="6182e-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6182e-601">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="6182e-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6182e-602">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="6182e-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6182e-603">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="6182e-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6182e-604">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="6182e-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6182e-605">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="6182e-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6182e-606">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="6182e-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6182e-607">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6182e-608">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="6182e-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6182e-609">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="6182e-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6182e-610">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="6182e-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6182e-611">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6182e-612">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="6182e-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6182e-613">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="6182e-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6182e-614">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="6182e-614">Generated clients</span></span>

<span data-ttu-id="6182e-615">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="6182e-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6182e-616">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="6182e-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6182e-617">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="6182e-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6182e-618">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="6182e-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6182e-619">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="6182e-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6182e-620">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="6182e-621">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="6182e-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6182e-622">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="6182e-622">Outgoing request middleware</span></span>

<span data-ttu-id="6182e-623">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6182e-624">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6182e-625">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6182e-626">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="6182e-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6182e-627">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6182e-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6182e-628">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="6182e-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6182e-629">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="6182e-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6182e-630">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="6182e-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6182e-631">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6182e-632">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="6182e-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6182e-633">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="6182e-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6182e-634">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="6182e-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6182e-635">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="6182e-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6182e-636">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="6182e-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6182e-637">处理程序必须在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="6182e-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="6182e-638">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="6182e-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="6182e-639">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="6182e-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="6182e-640">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="6182e-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="6182e-641">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="6182e-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="6182e-642">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6182e-643">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6182e-644">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="6182e-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6182e-645">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6182e-646">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6182e-647">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="6182e-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6182e-648">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-648">Use Polly-based handlers</span></span>

<span data-ttu-id="6182e-649">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="6182e-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6182e-650">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="6182e-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6182e-651">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="6182e-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6182e-652">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6182e-653">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="6182e-653">The Polly extensions:</span></span>

* <span data-ttu-id="6182e-654">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6182e-655">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="6182e-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6182e-656">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="6182e-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6182e-657">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="6182e-657">Handle transient faults</span></span>

<span data-ttu-id="6182e-658">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6182e-659">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="6182e-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6182e-660">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="6182e-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6182e-661">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="6182e-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6182e-662">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="6182e-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6182e-663">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6182e-664">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="6182e-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6182e-665">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="6182e-665">Dynamically select policies</span></span>

<span data-ttu-id="6182e-666">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6182e-667">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="6182e-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6182e-668">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="6182e-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6182e-669">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6182e-670">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="6182e-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6182e-671">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="6182e-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="6182e-672">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="6182e-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6182e-673">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6182e-674">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6182e-675">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="6182e-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6182e-676">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="6182e-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6182e-677">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="6182e-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6182e-678">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6182e-679">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6182e-680">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="6182e-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="6182e-681">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="6182e-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6182e-682">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="6182e-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6182e-683">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="6182e-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6182e-684">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="6182e-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6182e-685">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="6182e-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6182e-686">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="6182e-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="6182e-687">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-688">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="6182e-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6182e-689">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="6182e-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6182e-690">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="6182e-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6182e-691">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="6182e-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6182e-692">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="6182e-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6182e-693">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="6182e-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6182e-694">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="6182e-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6182e-695">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="6182e-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6182e-696">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="6182e-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6182e-697">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="6182e-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6182e-698">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="6182e-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="6182e-699">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6182e-700">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="6182e-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6182e-701">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6182e-702">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6182e-703">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="6182e-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6182e-704">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="6182e-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6182e-705">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="6182e-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6182e-706">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6182e-707">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6182e-708">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6182e-709">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="6182e-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6182e-710">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6182e-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6182e-711">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="6182e-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6182e-712">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6182e-713">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="6182e-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6182e-714">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="6182e-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6182e-715">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="6182e-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6182e-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="6182e-716">Cookies</span></span>

<span data-ttu-id="6182e-717">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="6182e-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6182e-718">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="6182e-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6182e-719">对于需要 cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6182e-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6182e-720">禁用自动 cookie 处理</span><span class="sxs-lookup"><span data-stu-id="6182e-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6182e-721">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6182e-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6182e-722">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="6182e-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6182e-723">Logging</span><span class="sxs-lookup"><span data-stu-id="6182e-723">Logging</span></span>

<span data-ttu-id="6182e-724">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6182e-725">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6182e-726">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="6182e-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6182e-727">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="6182e-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6182e-728">例如，名为“MyNamedClient”的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6182e-729">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6182e-730">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6182e-731">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6182e-732">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="6182e-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6182e-733">在“MyNamedClient”示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="6182e-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6182e-734">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="6182e-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6182e-735">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="6182e-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6182e-736">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6182e-737">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="6182e-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6182e-738">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="6182e-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6182e-739">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6182e-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6182e-740">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="6182e-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6182e-741">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="6182e-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6182e-742"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="6182e-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6182e-743">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="6182e-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6182e-744">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6182e-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6182e-745">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="6182e-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6182e-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6182e-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6182e-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6182e-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6182e-748">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="6182e-748">In the following example:</span></span>

* <span data-ttu-id="6182e-749"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="6182e-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6182e-750">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="6182e-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6182e-751">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="6182e-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6182e-752">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="6182e-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="6182e-753">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="6182e-753">Header propagation middleware</span></span>

<span data-ttu-id="6182e-754">标头传播是一个社区支持的中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="6182e-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="6182e-755">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="6182e-755">To use header propagation:</span></span>

* <span data-ttu-id="6182e-756">引用 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 包的社区支持的端口。</span><span class="sxs-lookup"><span data-stu-id="6182e-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="6182e-757">ASP.NET Core 3.1 及更高版本支持 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。</span><span class="sxs-lookup"><span data-stu-id="6182e-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="6182e-758">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="6182e-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="6182e-759">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="6182e-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="6182e-760">其他资源</span><span class="sxs-lookup"><span data-stu-id="6182e-760">Additional resources</span></span>

* [<span data-ttu-id="6182e-761">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="6182e-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6182e-762">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="6182e-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6182e-763">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="6182e-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
