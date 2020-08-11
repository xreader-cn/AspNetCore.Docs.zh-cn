---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: fb9001c06228b4290ca1e0c7cfb6b1338f431cd6
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444107"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="b498c-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="b498c-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b498c-104">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b498c-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="b498c-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b498c-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="b498c-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="b498c-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b498c-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="b498c-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b498c-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="b498c-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="b498c-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="b498c-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="b498c-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="b498c-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="b498c-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="b498c-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b498c-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b498c-115">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b498c-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b498c-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="b498c-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="b498c-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="b498c-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b498c-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="b498c-118">Consumption patterns</span></span>

<span data-ttu-id="b498c-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="b498c-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b498c-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b498c-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b498c-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b498c-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b498c-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="b498c-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b498c-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-125">Basic usage</span></span>

<span data-ttu-id="b498c-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="b498c-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b498c-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="b498c-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b498c-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="b498c-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b498c-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="b498c-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="b498c-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="b498c-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="b498c-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="b498c-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b498c-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-132">Named clients</span></span>

<span data-ttu-id="b498c-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="b498c-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="b498c-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="b498c-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="b498c-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="b498c-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="b498c-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="b498c-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b498c-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="b498c-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="b498c-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="b498c-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="b498c-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="b498c-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="b498c-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="b498c-140">CreateClient</span></span>

<span data-ttu-id="b498c-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="b498c-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="b498c-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="b498c-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="b498c-143">The configuration action is called.</span></span>

<span data-ttu-id="b498c-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="b498c-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b498c-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="b498c-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b498c-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="b498c-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b498c-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-147">Typed clients</span></span>

<span data-ttu-id="b498c-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-148">Typed clients:</span></span>

* <span data-ttu-id="b498c-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="b498c-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b498c-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="b498c-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b498c-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="b498c-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b498c-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="b498c-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="b498c-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="b498c-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="b498c-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="b498c-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="b498c-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="b498c-156">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="b498c-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b498c-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="b498c-157">In the preceding code:</span></span>

* <span data-ttu-id="b498c-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="b498c-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="b498c-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="b498c-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="b498c-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="b498c-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b498c-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="b498c-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="b498c-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b498c-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b498c-164">在上述代码中，`AddHttpClient` 将 `GitHubService` 注册为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="b498c-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="b498c-165">此注册使用工厂方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b498c-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="b498c-166">创建 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="b498c-167">创建 `GitHubService` 的实例，将 `HttpClient` 的实例传入其构造函数。</span><span class="sxs-lookup"><span data-stu-id="b498c-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="b498c-168">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b498c-169">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="b498c-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b498c-170">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="b498c-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="b498c-171">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="b498c-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b498c-172">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="b498c-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="b498c-173">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b498c-174">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-174">Generated clients</span></span>

<span data-ttu-id="b498c-175">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="b498c-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b498c-176">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="b498c-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b498c-177">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="b498c-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b498c-178">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="b498c-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b498c-179">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="b498c-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="b498c-180">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="b498c-181">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="b498c-182">发出 POST、PUT 和 DELETE 请求</span><span class="sxs-lookup"><span data-stu-id="b498c-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="b498c-183">在前面的示例中，所有 HTTP 请求均使用 GET HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="b498c-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="b498c-184">`HttpClient` 还支持其他 HTTP 谓词，其中包括：</span><span class="sxs-lookup"><span data-stu-id="b498c-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="b498c-185">POST</span><span class="sxs-lookup"><span data-stu-id="b498c-185">POST</span></span>
* <span data-ttu-id="b498c-186">PUT</span><span class="sxs-lookup"><span data-stu-id="b498c-186">PUT</span></span>
* <span data-ttu-id="b498c-187">删除</span><span class="sxs-lookup"><span data-stu-id="b498c-187">DELETE</span></span>
* <span data-ttu-id="b498c-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="b498c-188">PATCH</span></span>

<span data-ttu-id="b498c-189">有关受支持的 HTTP 谓词的完整列表，请参阅 <xref:System.Net.Http.HttpMethod>。</span><span class="sxs-lookup"><span data-stu-id="b498c-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="b498c-190">下面的示例演示如何发出 HTTP POST 请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="b498c-191">在前面的代码中，`CreateItemAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b498c-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="b498c-192">使用 `System.Text.Json` 将 `TodoItem` 参数序列化为 JSON。</span><span class="sxs-lookup"><span data-stu-id="b498c-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="b498c-193">这将使用 <xref:System.Text.Json.JsonSerializerOptions> 的实例来配置序列化过程。</span><span class="sxs-lookup"><span data-stu-id="b498c-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="b498c-194">创建 <xref:System.Net.Http.StringContent> 的实例，以打包序列化的 JSON 以便在 HTTP 请求的正文中发送。</span><span class="sxs-lookup"><span data-stu-id="b498c-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="b498c-195">调用 <xref:System.Net.Http.HttpClient.PostAsync%2A> 将 JSON 内容发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="b498c-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="b498c-196">这是添加到 [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) 的相对 URL。</span><span class="sxs-lookup"><span data-stu-id="b498c-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="b498c-197">如果响应状态代码不指示成功，则调用 <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> 引发异常。</span><span class="sxs-lookup"><span data-stu-id="b498c-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="b498c-198">`HttpClient` 还支持其他类型的内容。</span><span class="sxs-lookup"><span data-stu-id="b498c-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="b498c-199">例如，<xref:System.Net.Http.MultipartContent> 和 <xref:System.Net.Http.StreamContent>。</span><span class="sxs-lookup"><span data-stu-id="b498c-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="b498c-200">有关受支持的内容的完整列表，请参阅 <xref:System.Net.Http.HttpContent>。</span><span class="sxs-lookup"><span data-stu-id="b498c-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="b498c-201">下面的示例演示了一个 HTTP PUT 请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="b498c-202">前面的代码与 POST 示例非常相似。</span><span class="sxs-lookup"><span data-stu-id="b498c-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="b498c-203">`SaveItemAsync` 方法调用 <xref:System.Net.Http.HttpClient.PutAsync%2A> 而不是 `PostAsync`。</span><span class="sxs-lookup"><span data-stu-id="b498c-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="b498c-204">下面的示例演示了一个 HTTP DELETE 请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="b498c-205">在前面的代码中，`DeleteItemAsync` 方法调用 <xref:System.Net.Http.HttpClient.DeleteAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="b498c-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="b498c-206">由于 HTTP DELETE 请求通常不包含正文，因此 `DeleteAsync` 方法不提供接受 `HttpContent` 实例的重载。</span><span class="sxs-lookup"><span data-stu-id="b498c-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="b498c-207">要详细了解如何将不同的 HTTP 谓词用于 `HttpClient`，请参阅 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="b498c-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b498c-208">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="b498c-208">Outgoing request middleware</span></span>

<span data-ttu-id="b498c-209">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b498c-210">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="b498c-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="b498c-211">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="b498c-212">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b498c-213">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="b498c-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b498c-214">此模式：</span><span class="sxs-lookup"><span data-stu-id="b498c-214">This pattern:</span></span>

  * <span data-ttu-id="b498c-215">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="b498c-216">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="b498c-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="b498c-217">缓存</span><span class="sxs-lookup"><span data-stu-id="b498c-217">caching</span></span>
    * <span data-ttu-id="b498c-218">错误处理</span><span class="sxs-lookup"><span data-stu-id="b498c-218">error handling</span></span>
    * <span data-ttu-id="b498c-219">序列化</span><span class="sxs-lookup"><span data-stu-id="b498c-219">serialization</span></span>
    * <span data-ttu-id="b498c-220">日志记录</span><span class="sxs-lookup"><span data-stu-id="b498c-220">logging</span></span>

<span data-ttu-id="b498c-221">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="b498c-221">To create a delegating handler:</span></span>

* <span data-ttu-id="b498c-222">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="b498c-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="b498c-223">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="b498c-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="b498c-224">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="b498c-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b498c-225">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="b498c-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="b498c-226">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="b498c-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="b498c-227">可使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="b498c-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="b498c-228">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="b498c-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b498c-229">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="b498c-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="b498c-230">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b498c-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="b498c-231">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="b498c-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="b498c-232">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="b498c-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="b498c-233">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b498c-234">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b498c-235">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="b498c-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b498c-236">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="b498c-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="b498c-237">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="b498c-238">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="b498c-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b498c-239">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-239">Use Polly-based handlers</span></span>

<span data-ttu-id="b498c-240">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="b498c-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b498c-241">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="b498c-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b498c-242">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="b498c-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b498c-243">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b498c-244">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="b498c-245">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b498c-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b498c-246">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="b498c-246">Handle transient faults</span></span>

<span data-ttu-id="b498c-247">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b498c-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="b498c-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b498c-249">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="b498c-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="b498c-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="b498c-250">HTTP 5xx</span></span>
* <span data-ttu-id="b498c-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="b498c-251">HTTP 408</span></span>

<span data-ttu-id="b498c-252">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="b498c-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="b498c-253">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b498c-254">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="b498c-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b498c-255">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="b498c-255">Dynamically select policies</span></span>

<span data-ttu-id="b498c-256">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="b498c-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="b498c-257">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="b498c-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b498c-258">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b498c-259">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b498c-260">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="b498c-261">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="b498c-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b498c-262">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="b498c-262">In the preceding example:</span></span>

* <span data-ttu-id="b498c-263">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-263">Two handlers are added.</span></span>
* <span data-ttu-id="b498c-264">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="b498c-265">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="b498c-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="b498c-266">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="b498c-267">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="b498c-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="b498c-268">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b498c-269">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b498c-270">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="b498c-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="b498c-271">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="b498c-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="b498c-272">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="b498c-272">In the following code:</span></span>

* <span data-ttu-id="b498c-273">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="b498c-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="b498c-275">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="b498c-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b498c-276">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="b498c-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="b498c-277">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-278">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="b498c-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="b498c-279">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="b498c-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b498c-280">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="b498c-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b498c-281">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="b498c-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b498c-282">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b498c-283">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="b498c-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b498c-284">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="b498c-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="b498c-285">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="b498c-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b498c-286">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="b498c-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="b498c-287">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="b498c-288">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b498c-289">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="b498c-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="b498c-290">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-291">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b498c-292">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="b498c-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b498c-293">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="b498c-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b498c-294">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b498c-295">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b498c-296">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b498c-297">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="b498c-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b498c-298">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="b498c-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b498c-299">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b498c-300">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b498c-301">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="b498c-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b498c-302">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="b498c-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b498c-303">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b498c-304">Cookie</span><span class="sxs-lookup"><span data-stu-id="b498c-304">Cookies</span></span>

<span data-ttu-id="b498c-305">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b498c-306">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="b498c-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b498c-307">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="b498c-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b498c-308">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="b498c-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b498c-309">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b498c-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b498c-310">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="b498c-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b498c-311">Logging</span><span class="sxs-lookup"><span data-stu-id="b498c-311">Logging</span></span>

<span data-ttu-id="b498c-312">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b498c-313">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="b498c-314">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="b498c-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b498c-315">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="b498c-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b498c-316">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="b498c-317">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b498c-318">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b498c-319">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b498c-320">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b498c-321">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”。</span><span class="sxs-lookup"><span data-stu-id="b498c-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="b498c-322">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="b498c-323">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b498c-324">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b498c-325">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="b498c-326">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="b498c-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b498c-327">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b498c-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b498c-328">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="b498c-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b498c-329">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b498c-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b498c-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="b498c-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b498c-331">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="b498c-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b498c-332">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b498c-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b498c-333">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="b498c-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b498c-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b498c-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b498c-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b498c-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b498c-336">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="b498c-336">In the following example:</span></span>

* <span data-ttu-id="b498c-337"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="b498c-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b498c-338">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b498c-339">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="b498c-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b498c-340">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="b498c-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="b498c-341">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="b498c-341">Header propagation middleware</span></span>

<span data-ttu-id="b498c-342">标头传播是一个 ASP.NET Core 中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="b498c-343">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="b498c-343">To use header propagation:</span></span>

* <span data-ttu-id="b498c-344">引用 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 包。</span><span class="sxs-lookup"><span data-stu-id="b498c-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="b498c-345">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="b498c-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="b498c-346">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="b498c-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="b498c-347">其他资源</span><span class="sxs-lookup"><span data-stu-id="b498c-347">Additional resources</span></span>

* [<span data-ttu-id="b498c-348">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="b498c-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b498c-349">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="b498c-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b498c-350">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="b498c-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="b498c-351">如何在 .NET 中对 JSON 数据进行序列化和反序列化</span><span class="sxs-lookup"><span data-stu-id="b498c-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b498c-352">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="b498c-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="b498c-353">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b498c-354">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="b498c-354">It offers the following benefits:</span></span>

* <span data-ttu-id="b498c-355">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b498c-356">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="b498c-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b498c-357">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="b498c-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="b498c-358">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="b498c-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="b498c-359">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b498c-360">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b498c-361">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b498c-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b498c-362">消耗模式</span><span class="sxs-lookup"><span data-stu-id="b498c-362">Consumption patterns</span></span>

<span data-ttu-id="b498c-363">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="b498c-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b498c-364">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b498c-365">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b498c-366">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b498c-367">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b498c-368">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="b498c-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="b498c-369">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="b498c-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b498c-370">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-370">Basic usage</span></span>

<span data-ttu-id="b498c-371">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="b498c-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b498c-372">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="b498c-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b498c-373">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="b498c-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b498c-374">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="b498c-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="b498c-375">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="b498c-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="b498c-376">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="b498c-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b498c-377">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-377">Named clients</span></span>

<span data-ttu-id="b498c-378">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="b498c-379">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="b498c-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b498c-380">上面的代码调用 `AddHttpClient`，同时提供名称“github”。</span><span class="sxs-lookup"><span data-stu-id="b498c-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="b498c-381">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="b498c-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="b498c-382">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="b498c-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="b498c-383">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="b498c-384">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="b498c-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b498c-385">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="b498c-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b498c-386">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="b498c-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b498c-387">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-387">Typed clients</span></span>

<span data-ttu-id="b498c-388">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-388">Typed clients:</span></span>

* <span data-ttu-id="b498c-389">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="b498c-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b498c-390">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="b498c-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b498c-391">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="b498c-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b498c-392">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="b498c-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="b498c-393">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="b498c-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="b498c-394">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="b498c-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="b498c-395">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="b498c-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="b498c-396">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="b498c-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="b498c-397">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="b498c-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b498c-398">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="b498c-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="b498c-399">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="b498c-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b498c-400">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b498c-401">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b498c-402">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="b498c-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b498c-403">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="b498c-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="b498c-404">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b498c-405">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="b498c-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="b498c-406">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="b498c-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b498c-407">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-407">Generated clients</span></span>

<span data-ttu-id="b498c-408">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="b498c-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b498c-409">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="b498c-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b498c-410">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="b498c-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b498c-411">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="b498c-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b498c-412">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="b498c-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="b498c-413">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="b498c-414">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b498c-415">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="b498c-415">Outgoing request middleware</span></span>

<span data-ttu-id="b498c-416">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b498c-417">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="b498c-418">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b498c-419">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="b498c-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b498c-420">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="b498c-421">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="b498c-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="b498c-422">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="b498c-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="b498c-423">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="b498c-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b498c-424">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="b498c-425">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="b498c-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="b498c-426">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="b498c-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="b498c-427">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="b498c-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="b498c-428">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="b498c-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="b498c-429">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="b498c-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b498c-430">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="b498c-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="b498c-431">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b498c-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="b498c-432">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="b498c-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="b498c-433">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="b498c-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="b498c-434">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b498c-435">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b498c-436">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="b498c-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b498c-437">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="b498c-438">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="b498c-439">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="b498c-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b498c-440">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-440">Use Polly-based handlers</span></span>

<span data-ttu-id="b498c-441">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="b498c-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b498c-442">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="b498c-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b498c-443">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="b498c-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b498c-444">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b498c-445">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="b498c-445">The Polly extensions:</span></span>

* <span data-ttu-id="b498c-446">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="b498c-447">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="b498c-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="b498c-448">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="b498c-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b498c-449">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="b498c-449">Handle transient faults</span></span>

<span data-ttu-id="b498c-450">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b498c-451">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="b498c-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b498c-452">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="b498c-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="b498c-453">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="b498c-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b498c-454">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="b498c-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="b498c-455">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b498c-456">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="b498c-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b498c-457">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="b498c-457">Dynamically select policies</span></span>

<span data-ttu-id="b498c-458">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="b498c-459">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="b498c-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="b498c-460">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b498c-461">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b498c-462">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b498c-463">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="b498c-464">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="b498c-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b498c-465">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="b498c-466">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="b498c-467">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="b498c-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="b498c-468">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="b498c-469">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="b498c-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="b498c-470">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b498c-471">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b498c-472">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="b498c-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="b498c-473">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="b498c-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="b498c-474">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="b498c-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="b498c-475">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="b498c-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="b498c-476">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="b498c-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="b498c-477">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="b498c-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b498c-478">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="b498c-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="b498c-479">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-480">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="b498c-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="b498c-481">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="b498c-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b498c-482">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="b498c-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b498c-483">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="b498c-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b498c-484">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b498c-485">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="b498c-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b498c-486">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="b498c-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="b498c-487">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="b498c-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b498c-488">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="b498c-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="b498c-489">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="b498c-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="b498c-490">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="b498c-491">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b498c-492">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="b498c-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="b498c-493">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="b498c-494">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-495">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b498c-496">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="b498c-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b498c-497">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="b498c-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b498c-498">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b498c-499">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b498c-500">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b498c-501">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="b498c-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b498c-502">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="b498c-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b498c-503">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b498c-504">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b498c-505">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="b498c-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b498c-506">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="b498c-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b498c-507">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b498c-508">Cookie</span><span class="sxs-lookup"><span data-stu-id="b498c-508">Cookies</span></span>

<span data-ttu-id="b498c-509">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b498c-510">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="b498c-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b498c-511">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="b498c-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b498c-512">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="b498c-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b498c-513">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b498c-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b498c-514">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="b498c-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b498c-515">Logging</span><span class="sxs-lookup"><span data-stu-id="b498c-515">Logging</span></span>

<span data-ttu-id="b498c-516">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b498c-517">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="b498c-518">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="b498c-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b498c-519">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="b498c-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b498c-520">例如，名为“MyNamedClient”的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="b498c-521">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b498c-522">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b498c-523">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b498c-524">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b498c-525">在“MyNamedClient”示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="b498c-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="b498c-526">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="b498c-527">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b498c-528">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b498c-529">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="b498c-530">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="b498c-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b498c-531">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b498c-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b498c-532">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="b498c-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b498c-533">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b498c-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b498c-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="b498c-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b498c-535">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="b498c-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b498c-536">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b498c-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b498c-537">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="b498c-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b498c-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b498c-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b498c-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b498c-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b498c-540">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="b498c-540">In the following example:</span></span>

* <span data-ttu-id="b498c-541"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="b498c-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b498c-542">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b498c-543">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="b498c-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b498c-544">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="b498c-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="b498c-545">其他资源</span><span class="sxs-lookup"><span data-stu-id="b498c-545">Additional resources</span></span>

* [<span data-ttu-id="b498c-546">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="b498c-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b498c-547">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="b498c-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b498c-548">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="b498c-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b498c-549">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="b498c-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="b498c-550">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b498c-551">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="b498c-551">It offers the following benefits:</span></span>

* <span data-ttu-id="b498c-552">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b498c-553">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="b498c-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b498c-554">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="b498c-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="b498c-555">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="b498c-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="b498c-556">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b498c-557">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b498c-558">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b498c-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b498c-559">先决条件</span><span class="sxs-lookup"><span data-stu-id="b498c-559">Prerequisites</span></span>

<span data-ttu-id="b498c-560">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b498c-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="b498c-561">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="b498c-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b498c-562">消耗模式</span><span class="sxs-lookup"><span data-stu-id="b498c-562">Consumption patterns</span></span>

<span data-ttu-id="b498c-563">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="b498c-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b498c-564">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b498c-565">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b498c-566">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b498c-567">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b498c-568">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="b498c-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="b498c-569">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="b498c-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b498c-570">基本用法</span><span class="sxs-lookup"><span data-stu-id="b498c-570">Basic usage</span></span>

<span data-ttu-id="b498c-571">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="b498c-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b498c-572">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="b498c-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b498c-573">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="b498c-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b498c-574">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="b498c-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="b498c-575">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="b498c-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="b498c-576">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="b498c-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b498c-577">命名客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-577">Named clients</span></span>

<span data-ttu-id="b498c-578">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="b498c-579">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="b498c-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b498c-580">上面的代码调用 `AddHttpClient`，同时提供名称“github”。</span><span class="sxs-lookup"><span data-stu-id="b498c-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="b498c-581">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="b498c-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="b498c-582">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="b498c-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="b498c-583">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="b498c-584">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="b498c-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b498c-585">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="b498c-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b498c-586">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="b498c-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b498c-587">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-587">Typed clients</span></span>

<span data-ttu-id="b498c-588">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-588">Typed clients:</span></span>

* <span data-ttu-id="b498c-589">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="b498c-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b498c-590">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="b498c-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b498c-591">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="b498c-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b498c-592">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="b498c-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="b498c-593">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="b498c-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="b498c-594">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="b498c-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="b498c-595">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="b498c-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="b498c-596">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="b498c-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="b498c-597">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="b498c-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b498c-598">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="b498c-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="b498c-599">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="b498c-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b498c-600">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b498c-601">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="b498c-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b498c-602">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="b498c-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b498c-603">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="b498c-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="b498c-604">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b498c-605">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="b498c-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="b498c-606">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="b498c-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b498c-607">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="b498c-607">Generated clients</span></span>

<span data-ttu-id="b498c-608">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="b498c-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b498c-609">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="b498c-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b498c-610">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="b498c-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b498c-611">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="b498c-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b498c-612">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="b498c-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="b498c-613">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="b498c-614">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="b498c-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b498c-615">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="b498c-615">Outgoing request middleware</span></span>

<span data-ttu-id="b498c-616">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b498c-617">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="b498c-618">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b498c-619">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="b498c-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b498c-620">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="b498c-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="b498c-621">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="b498c-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="b498c-622">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="b498c-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="b498c-623">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="b498c-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b498c-624">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="b498c-625">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="b498c-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="b498c-626">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="b498c-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="b498c-627">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="b498c-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="b498c-628">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="b498c-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="b498c-629">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="b498c-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b498c-630">处理程序必须在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="b498c-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="b498c-631">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="b498c-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="b498c-632">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="b498c-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="b498c-633">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="b498c-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="b498c-634">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="b498c-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="b498c-635">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b498c-636">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b498c-637">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="b498c-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b498c-638">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="b498c-639">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="b498c-640">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="b498c-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b498c-641">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-641">Use Polly-based handlers</span></span>

<span data-ttu-id="b498c-642">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="b498c-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b498c-643">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="b498c-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b498c-644">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="b498c-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b498c-645">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b498c-646">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="b498c-646">The Polly extensions:</span></span>

* <span data-ttu-id="b498c-647">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="b498c-648">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="b498c-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="b498c-649">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="b498c-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b498c-650">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="b498c-650">Handle transient faults</span></span>

<span data-ttu-id="b498c-651">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b498c-652">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="b498c-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b498c-653">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="b498c-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="b498c-654">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="b498c-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b498c-655">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="b498c-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="b498c-656">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b498c-657">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="b498c-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b498c-658">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="b498c-658">Dynamically select policies</span></span>

<span data-ttu-id="b498c-659">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="b498c-660">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="b498c-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="b498c-661">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="b498c-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b498c-662">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b498c-663">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="b498c-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b498c-664">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="b498c-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="b498c-665">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="b498c-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b498c-666">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="b498c-667">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="b498c-668">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="b498c-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="b498c-669">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="b498c-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="b498c-670">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="b498c-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="b498c-671">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b498c-672">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b498c-673">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="b498c-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="b498c-674">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="b498c-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="b498c-675">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="b498c-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="b498c-676">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="b498c-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="b498c-677">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="b498c-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="b498c-678">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="b498c-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b498c-679">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="b498c-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="b498c-680">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-681">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="b498c-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="b498c-682">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="b498c-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b498c-683">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="b498c-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b498c-684">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="b498c-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b498c-685">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="b498c-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b498c-686">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="b498c-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b498c-687">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="b498c-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="b498c-688">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="b498c-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b498c-689">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="b498c-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="b498c-690">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="b498c-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="b498c-691">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="b498c-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="b498c-692">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b498c-693">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="b498c-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="b498c-694">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="b498c-695">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b498c-696">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="b498c-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b498c-697">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="b498c-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b498c-698">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="b498c-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b498c-699">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b498c-700">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b498c-701">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b498c-702">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="b498c-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b498c-703">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="b498c-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b498c-704">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="b498c-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b498c-705">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b498c-706">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="b498c-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b498c-707">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="b498c-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b498c-708">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="b498c-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b498c-709">Cookie</span><span class="sxs-lookup"><span data-stu-id="b498c-709">Cookies</span></span>

<span data-ttu-id="b498c-710">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="b498c-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b498c-711">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="b498c-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b498c-712">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="b498c-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b498c-713">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="b498c-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b498c-714">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b498c-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b498c-715">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="b498c-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b498c-716">Logging</span><span class="sxs-lookup"><span data-stu-id="b498c-716">Logging</span></span>

<span data-ttu-id="b498c-717">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b498c-718">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="b498c-719">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="b498c-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b498c-720">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="b498c-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b498c-721">例如，名为“MyNamedClient”的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="b498c-722">后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b498c-723">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b498c-724">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b498c-725">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="b498c-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b498c-726">在“MyNamedClient”示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="b498c-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="b498c-727">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="b498c-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="b498c-728">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="b498c-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b498c-729">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b498c-730">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="b498c-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="b498c-731">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="b498c-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b498c-732">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b498c-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b498c-733">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="b498c-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b498c-734">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b498c-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b498c-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="b498c-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b498c-736">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="b498c-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b498c-737">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b498c-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b498c-738">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="b498c-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b498c-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b498c-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b498c-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b498c-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b498c-741">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="b498c-741">In the following example:</span></span>

* <span data-ttu-id="b498c-742"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="b498c-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b498c-743">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="b498c-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b498c-744">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="b498c-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b498c-745">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="b498c-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="b498c-746">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="b498c-746">Header propagation middleware</span></span>

<span data-ttu-id="b498c-747">标头传播是一个社区支持的中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="b498c-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="b498c-748">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="b498c-748">To use header propagation:</span></span>

* <span data-ttu-id="b498c-749">引用 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 包的社区支持的端口。</span><span class="sxs-lookup"><span data-stu-id="b498c-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="b498c-750">ASP.NET Core 3.1 及更高版本支持 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。</span><span class="sxs-lookup"><span data-stu-id="b498c-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="b498c-751">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="b498c-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="b498c-752">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="b498c-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="b498c-753">其他资源</span><span class="sxs-lookup"><span data-stu-id="b498c-753">Additional resources</span></span>

* [<span data-ttu-id="b498c-754">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="b498c-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b498c-755">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="b498c-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b498c-756">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="b498c-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
