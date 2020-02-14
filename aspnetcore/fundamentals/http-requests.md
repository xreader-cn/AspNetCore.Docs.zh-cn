---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 93b75525e8a3f10c4e0b655baaff83c0f6e8131b
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171803"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="7a4a1-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="7a4a1-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a4a1-104">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7a4a1-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="7a4a1-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7a4a1-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="7a4a1-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7a4a1-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="7a4a1-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="7a4a1-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="7a4a1-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="7a4a1-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7a4a1-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7a4a1-115">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7a4a1-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="7a4a1-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7a4a1-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-118">Consumption patterns</span></span>

<span data-ttu-id="7a4a1-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7a4a1-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7a4a1-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7a4a1-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7a4a1-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7a4a1-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7a4a1-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-125">Basic usage</span></span>

<span data-ttu-id="7a4a1-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7a4a1-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7a4a1-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7a4a1-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="7a4a1-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="7a4a1-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7a4a1-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-132">Named clients</span></span>

<span data-ttu-id="7a4a1-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="7a4a1-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="7a4a1-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="7a4a1-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7a4a1-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="7a4a1-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="7a4a1-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="7a4a1-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="7a4a1-140">CreateClient</span></span>

<span data-ttu-id="7a4a1-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="7a4a1-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="7a4a1-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-143">The configuration action is called.</span></span>

<span data-ttu-id="7a4a1-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7a4a1-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7a4a1-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7a4a1-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-147">Typed clients</span></span>

<span data-ttu-id="7a4a1-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-148">Typed clients:</span></span>

* <span data-ttu-id="7a4a1-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7a4a1-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7a4a1-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7a4a1-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="7a4a1-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="7a4a1-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="7a4a1-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="7a4a1-156">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7a4a1-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-157">In the preceding code:</span></span>

* <span data-ttu-id="7a4a1-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="7a4a1-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="7a4a1-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7a4a1-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="7a4a1-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7a4a1-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7a4a1-164">在上述代码中，`AddHttpClient` 将 `GitHubService` 注册为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="7a4a1-165">此注册使用工厂方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="7a4a1-166">创建 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="7a4a1-167">创建 `GitHubService` 的实例，将 `HttpClient` 的实例传入其构造函数。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="7a4a1-168">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7a4a1-169">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7a4a1-170">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="7a4a1-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="7a4a1-171">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7a4a1-172">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="7a4a1-173">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7a4a1-174">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-174">Generated clients</span></span>

<span data-ttu-id="7a4a1-175">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7a4a1-176">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7a4a1-177">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7a4a1-178">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7a4a1-179">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7a4a1-180">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7a4a1-181">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7a4a1-182">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-182">Outgoing request middleware</span></span>

<span data-ttu-id="7a4a1-183">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7a4a1-184">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="7a4a1-185">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="7a4a1-186">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7a4a1-187">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7a4a1-188">此模式：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-188">This pattern:</span></span>

  * <span data-ttu-id="7a4a1-189">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="7a4a1-190">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="7a4a1-191">缓存</span><span class="sxs-lookup"><span data-stu-id="7a4a1-191">caching</span></span>
    * <span data-ttu-id="7a4a1-192">错误处理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-192">error handling</span></span>
    * <span data-ttu-id="7a4a1-193">序列化</span><span class="sxs-lookup"><span data-stu-id="7a4a1-193">serialization</span></span>
    * <span data-ttu-id="7a4a1-194">日志记录</span><span class="sxs-lookup"><span data-stu-id="7a4a1-194">logging</span></span>

<span data-ttu-id="7a4a1-195">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-195">To create a delegating handler:</span></span>

* <span data-ttu-id="7a4a1-196">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="7a4a1-197">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="7a4a1-198">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7a4a1-199">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="7a4a1-200">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="7a4a1-201">可使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="7a4a1-202">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7a4a1-203">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="7a4a1-204">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="7a4a1-205">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="7a4a1-206">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="7a4a1-207">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7a4a1-208">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7a4a1-209">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7a4a1-210">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="7a4a1-211">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="7a4a1-212">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7a4a1-213">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-213">Use Polly-based handlers</span></span>

<span data-ttu-id="7a4a1-214">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7a4a1-215">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7a4a1-216">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7a4a1-217">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-218">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="7a4a1-219">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7a4a1-220">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="7a4a1-220">Handle transient faults</span></span>

<span data-ttu-id="7a4a1-221">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7a4a1-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7a4a1-223">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="7a4a1-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="7a4a1-224">HTTP 5xx</span></span>
* <span data-ttu-id="7a4a1-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="7a4a1-225">HTTP 408</span></span>

<span data-ttu-id="7a4a1-226">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="7a4a1-227">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7a4a1-228">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7a4a1-229">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-229">Dynamically select policies</span></span>

<span data-ttu-id="7a4a1-230">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="7a4a1-231">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7a4a1-232">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7a4a1-233">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7a4a1-234">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="7a4a1-235">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7a4a1-236">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-236">In the preceding example:</span></span>

* <span data-ttu-id="7a4a1-237">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-237">Two handlers are added.</span></span>
* <span data-ttu-id="7a4a1-238">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="7a4a1-239">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="7a4a1-240">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="7a4a1-241">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="7a4a1-242">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7a4a1-243">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7a4a1-244">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="7a4a1-245">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="7a4a1-246">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-246">In the following code:</span></span>

* <span data-ttu-id="7a4a1-247">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="7a4a1-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="7a4a1-249">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7a4a1-250">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="7a4a1-251">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-252">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="7a4a1-253">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7a4a1-254">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7a4a1-255">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7a4a1-256">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7a4a1-257">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7a4a1-258">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="7a4a1-259">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7a4a1-260">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="7a4a1-261">`HttpClient` 实例通常可视为无需处置的 .NET 对象  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="7a4a1-262">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7a4a1-263">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="7a4a1-264">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-265">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7a4a1-266">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="7a4a1-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7a4a1-267">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7a4a1-268">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7a4a1-269">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7a4a1-270">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7a4a1-271">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7a4a1-272">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7a4a1-273">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7a4a1-274">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7a4a1-275">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-276">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7a4a1-277">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="7a4a1-278">Cookie</span><span class="sxs-lookup"><span data-stu-id="7a4a1-278">Cookies</span></span>

<span data-ttu-id="7a4a1-279">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7a4a1-280">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7a4a1-281">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7a4a1-282">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7a4a1-283">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7a4a1-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7a4a1-284">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7a4a1-285">Logging</span><span class="sxs-lookup"><span data-stu-id="7a4a1-285">Logging</span></span>

<span data-ttu-id="7a4a1-286">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7a4a1-287">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="7a4a1-288">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7a4a1-289">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7a4a1-290">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息   。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="7a4a1-291">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-292">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7a4a1-293">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7a4a1-294">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-295">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”   。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="7a4a1-296">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="7a4a1-297">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7a4a1-298">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7a4a1-299">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="7a4a1-300">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7a4a1-301">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7a4a1-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7a4a1-302">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7a4a1-303">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7a4a1-304"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7a4a1-305">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7a4a1-306">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7a4a1-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7a4a1-307">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7a4a1-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7a4a1-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7a4a1-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7a4a1-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7a4a1-310">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-310">In the following example:</span></span>

* <span data-ttu-id="7a4a1-311"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7a4a1-312">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7a4a1-313">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7a4a1-314">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="7a4a1-315">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-315">Header propagation middleware</span></span>

<span data-ttu-id="7a4a1-316">标头传播是一个 ASP.NET Core 中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="7a4a1-317">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-317">To use header propagation:</span></span>

* <span data-ttu-id="7a4a1-318">引用 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="7a4a1-319">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="7a4a1-320">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="7a4a1-321">其他资源</span><span class="sxs-lookup"><span data-stu-id="7a4a1-321">Additional resources</span></span>

* [<span data-ttu-id="7a4a1-322">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="7a4a1-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7a4a1-323">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="7a4a1-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7a4a1-324">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="7a4a1-325">如何在 .NET 中对 JSON 数据进行序列化和反序列化</span><span class="sxs-lookup"><span data-stu-id="7a4a1-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7a4a1-326">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="7a4a1-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="7a4a1-327">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7a4a1-328">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-328">It offers the following benefits:</span></span>

* <span data-ttu-id="7a4a1-329">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-330">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7a4a1-331">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="7a4a1-332">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="7a4a1-333">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7a4a1-334">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7a4a1-335">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7a4a1-335">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7a4a1-336">消耗模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-336">Consumption patterns</span></span>

<span data-ttu-id="7a4a1-337">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7a4a1-338">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7a4a1-339">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7a4a1-340">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7a4a1-341">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7a4a1-342">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="7a4a1-343">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7a4a1-344">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-344">Basic usage</span></span>

<span data-ttu-id="7a4a1-345">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7a4a1-346">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7a4a1-347">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7a4a1-348">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="7a4a1-349">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="7a4a1-350">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7a4a1-351">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-351">Named clients</span></span>

<span data-ttu-id="7a4a1-352">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="7a4a1-353">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7a4a1-354">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="7a4a1-355">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="7a4a1-356">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="7a4a1-357">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="7a4a1-358">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7a4a1-359">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7a4a1-360">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7a4a1-361">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-361">Typed clients</span></span>

<span data-ttu-id="7a4a1-362">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-362">Typed clients:</span></span>

* <span data-ttu-id="7a4a1-363">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7a4a1-364">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7a4a1-365">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7a4a1-366">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="7a4a1-367">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="7a4a1-368">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7a4a1-369">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="7a4a1-370">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="7a4a1-371">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7a4a1-372">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="7a4a1-373">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7a4a1-374">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7a4a1-375">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7a4a1-376">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7a4a1-377">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="7a4a1-378">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7a4a1-379">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="7a4a1-380">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7a4a1-381">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-381">Generated clients</span></span>

<span data-ttu-id="7a4a1-382">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7a4a1-383">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7a4a1-384">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7a4a1-385">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7a4a1-386">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7a4a1-387">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7a4a1-388">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7a4a1-389">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-389">Outgoing request middleware</span></span>

<span data-ttu-id="7a4a1-390">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7a4a1-391">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="7a4a1-392">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7a4a1-393">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7a4a1-394">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="7a4a1-395">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="7a4a1-396">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="7a4a1-397">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7a4a1-398">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="7a4a1-399">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="7a4a1-400">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="7a4a1-401">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="7a4a1-402">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="7a4a1-403">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7a4a1-404">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="7a4a1-405">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="7a4a1-406">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="7a4a1-407">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="7a4a1-408">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7a4a1-409">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7a4a1-410">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7a4a1-411">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="7a4a1-412">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="7a4a1-413">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7a4a1-414">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-414">Use Polly-based handlers</span></span>

<span data-ttu-id="7a4a1-415">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7a4a1-416">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7a4a1-417">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7a4a1-418">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-419">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-419">The Polly extensions:</span></span>

* <span data-ttu-id="7a4a1-420">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="7a4a1-421">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="7a4a1-422">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7a4a1-423">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="7a4a1-423">Handle transient faults</span></span>

<span data-ttu-id="7a4a1-424">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7a4a1-425">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7a4a1-426">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="7a4a1-427">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a4a1-428">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="7a4a1-429">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7a4a1-430">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7a4a1-431">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-431">Dynamically select policies</span></span>

<span data-ttu-id="7a4a1-432">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="7a4a1-433">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="7a4a1-434">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7a4a1-435">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7a4a1-436">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7a4a1-437">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="7a4a1-438">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7a4a1-439">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="7a4a1-440">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="7a4a1-441">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="7a4a1-442">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="7a4a1-443">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="7a4a1-444">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7a4a1-445">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7a4a1-446">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="7a4a1-447">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="7a4a1-448">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="7a4a1-449">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="7a4a1-450">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="7a4a1-451">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7a4a1-452">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="7a4a1-453">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-454">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="7a4a1-455">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7a4a1-456">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7a4a1-457">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7a4a1-458">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7a4a1-459">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7a4a1-460">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="7a4a1-461">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7a4a1-462">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="7a4a1-463">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="7a4a1-464">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="7a4a1-465">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7a4a1-466">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-467">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="7a4a1-468">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-469">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7a4a1-470">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="7a4a1-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7a4a1-471">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7a4a1-472">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7a4a1-473">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7a4a1-474">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7a4a1-475">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7a4a1-476">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7a4a1-477">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7a4a1-478">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7a4a1-479">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-480">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7a4a1-481">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="7a4a1-482">Cookie</span><span class="sxs-lookup"><span data-stu-id="7a4a1-482">Cookies</span></span>

<span data-ttu-id="7a4a1-483">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7a4a1-484">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7a4a1-485">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7a4a1-486">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7a4a1-487">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7a4a1-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7a4a1-488">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7a4a1-489">Logging</span><span class="sxs-lookup"><span data-stu-id="7a4a1-489">Logging</span></span>

<span data-ttu-id="7a4a1-490">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7a4a1-491">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="7a4a1-492">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7a4a1-493">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7a4a1-494">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="7a4a1-495">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-496">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7a4a1-497">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7a4a1-498">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-499">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="7a4a1-500">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="7a4a1-501">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7a4a1-502">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7a4a1-503">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="7a4a1-504">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7a4a1-505">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7a4a1-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7a4a1-506">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7a4a1-507">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7a4a1-508"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7a4a1-509">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7a4a1-510">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7a4a1-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7a4a1-511">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7a4a1-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7a4a1-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7a4a1-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7a4a1-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7a4a1-514">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-514">In the following example:</span></span>

* <span data-ttu-id="7a4a1-515"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7a4a1-516">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7a4a1-517">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7a4a1-518">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="7a4a1-519">其他资源</span><span class="sxs-lookup"><span data-stu-id="7a4a1-519">Additional resources</span></span>

* [<span data-ttu-id="7a4a1-520">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="7a4a1-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7a4a1-521">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="7a4a1-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7a4a1-522">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7a4a1-523">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="7a4a1-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="7a4a1-524">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7a4a1-525">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-525">It offers the following benefits:</span></span>

* <span data-ttu-id="7a4a1-526">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-527">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7a4a1-528">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="7a4a1-529">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="7a4a1-530">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7a4a1-531">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7a4a1-532">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7a4a1-532">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a4a1-533">先决条件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-533">Prerequisites</span></span>

<span data-ttu-id="7a4a1-534">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="7a4a1-535">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7a4a1-536">消耗模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-536">Consumption patterns</span></span>

<span data-ttu-id="7a4a1-537">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7a4a1-538">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7a4a1-539">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7a4a1-540">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7a4a1-541">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7a4a1-542">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="7a4a1-543">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7a4a1-544">基本用法</span><span class="sxs-lookup"><span data-stu-id="7a4a1-544">Basic usage</span></span>

<span data-ttu-id="7a4a1-545">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7a4a1-546">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7a4a1-547">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7a4a1-548">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="7a4a1-549">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="7a4a1-550">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7a4a1-551">命名客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-551">Named clients</span></span>

<span data-ttu-id="7a4a1-552">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="7a4a1-553">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7a4a1-554">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="7a4a1-555">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="7a4a1-556">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="7a4a1-557">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="7a4a1-558">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7a4a1-559">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7a4a1-560">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7a4a1-561">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-561">Typed clients</span></span>

<span data-ttu-id="7a4a1-562">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-562">Typed clients:</span></span>

* <span data-ttu-id="7a4a1-563">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7a4a1-564">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7a4a1-565">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7a4a1-566">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="7a4a1-567">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="7a4a1-568">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7a4a1-569">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="7a4a1-570">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="7a4a1-571">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7a4a1-572">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="7a4a1-573">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7a4a1-574">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7a4a1-575">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7a4a1-576">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7a4a1-577">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="7a4a1-578">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7a4a1-579">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="7a4a1-580">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7a4a1-581">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="7a4a1-581">Generated clients</span></span>

<span data-ttu-id="7a4a1-582">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7a4a1-583">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7a4a1-584">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7a4a1-585">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7a4a1-586">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7a4a1-587">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7a4a1-588">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7a4a1-589">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-589">Outgoing request middleware</span></span>

<span data-ttu-id="7a4a1-590">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7a4a1-591">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="7a4a1-592">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7a4a1-593">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7a4a1-594">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="7a4a1-595">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="7a4a1-596">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="7a4a1-597">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7a4a1-598">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="7a4a1-599">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="7a4a1-600">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="7a4a1-601">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="7a4a1-602">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="7a4a1-603">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7a4a1-604">处理程序必须  在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="7a4a1-605">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="7a4a1-606">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="7a4a1-607">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="7a4a1-608">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="7a4a1-609">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7a4a1-610">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7a4a1-611">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7a4a1-612">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="7a4a1-613">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="7a4a1-614">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7a4a1-615">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-615">Use Polly-based handlers</span></span>

<span data-ttu-id="7a4a1-616">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7a4a1-617">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7a4a1-618">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7a4a1-619">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-620">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-620">The Polly extensions:</span></span>

* <span data-ttu-id="7a4a1-621">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="7a4a1-622">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="7a4a1-623">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7a4a1-624">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="7a4a1-624">Handle transient faults</span></span>

<span data-ttu-id="7a4a1-625">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7a4a1-626">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7a4a1-627">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="7a4a1-628">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a4a1-629">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="7a4a1-630">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7a4a1-631">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7a4a1-632">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-632">Dynamically select policies</span></span>

<span data-ttu-id="7a4a1-633">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="7a4a1-634">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="7a4a1-635">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7a4a1-636">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7a4a1-637">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7a4a1-638">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="7a4a1-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="7a4a1-639">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7a4a1-640">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="7a4a1-641">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="7a4a1-642">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="7a4a1-643">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="7a4a1-644">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="7a4a1-645">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7a4a1-646">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7a4a1-647">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="7a4a1-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="7a4a1-648">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="7a4a1-649">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="7a4a1-650">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="7a4a1-651">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="7a4a1-652">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7a4a1-653">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="7a4a1-654">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-655">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="7a4a1-656">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7a4a1-657">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7a4a1-658">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7a4a1-659">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7a4a1-660">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7a4a1-661">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="7a4a1-662">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7a4a1-663">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="7a4a1-664">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="7a4a1-665">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="7a4a1-666">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7a4a1-667">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-668">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="7a4a1-669">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7a4a1-670">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7a4a1-671">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="7a4a1-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7a4a1-672">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7a4a1-673">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7a4a1-674">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7a4a1-675">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7a4a1-676">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7a4a1-677">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7a4a1-678">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7a4a1-679">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7a4a1-680">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7a4a1-681">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7a4a1-682">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="7a4a1-683">Cookie</span><span class="sxs-lookup"><span data-stu-id="7a4a1-683">Cookies</span></span>

<span data-ttu-id="7a4a1-684">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7a4a1-685">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7a4a1-686">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7a4a1-687">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="7a4a1-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7a4a1-688">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7a4a1-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7a4a1-689">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7a4a1-690">Logging</span><span class="sxs-lookup"><span data-stu-id="7a4a1-690">Logging</span></span>

<span data-ttu-id="7a4a1-691">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7a4a1-692">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="7a4a1-693">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7a4a1-694">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7a4a1-695">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="7a4a1-696">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-697">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7a4a1-698">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7a4a1-699">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7a4a1-700">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="7a4a1-701">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="7a4a1-702">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7a4a1-703">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7a4a1-704">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="7a4a1-705">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7a4a1-706">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7a4a1-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7a4a1-707">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7a4a1-708">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7a4a1-709"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7a4a1-710">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7a4a1-711">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7a4a1-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7a4a1-712">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7a4a1-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7a4a1-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7a4a1-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7a4a1-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7a4a1-715">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-715">In the following example:</span></span>

* <span data-ttu-id="7a4a1-716"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7a4a1-717">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7a4a1-718">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7a4a1-719">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="7a4a1-720">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="7a4a1-720">Header propagation middleware</span></span>

<span data-ttu-id="7a4a1-721">标头传播是一个社区支持的中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="7a4a1-722">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-722">To use header propagation:</span></span>

* <span data-ttu-id="7a4a1-723">引用 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 包的社区支持的端口。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="7a4a1-724">ASP.NET Core 3.1 及更高版本支持 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。</span><span class="sxs-lookup"><span data-stu-id="7a4a1-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="7a4a1-725">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="7a4a1-726">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="7a4a1-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="7a4a1-727">其他资源</span><span class="sxs-lookup"><span data-stu-id="7a4a1-727">Additional resources</span></span>

* [<span data-ttu-id="7a4a1-728">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="7a4a1-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7a4a1-729">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="7a4a1-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7a4a1-730">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="7a4a1-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
