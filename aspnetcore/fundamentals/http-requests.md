---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: f33444b8fc08dc022da7700af53a218600290162
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74733916"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="77582-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="77582-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77582-104">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="77582-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="77582-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="77582-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="77582-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="77582-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="77582-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="77582-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="77582-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="77582-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="77582-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="77582-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="77582-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="77582-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="77582-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="77582-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="77582-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="77582-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="77582-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="77582-115">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="77582-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="77582-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="77582-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="77582-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="77582-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="77582-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="77582-118">Consumption patterns</span></span>

<span data-ttu-id="77582-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="77582-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="77582-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="77582-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="77582-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="77582-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="77582-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="77582-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="77582-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-125">Basic usage</span></span>

<span data-ttu-id="77582-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="77582-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="77582-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="77582-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="77582-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="77582-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="77582-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="77582-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="77582-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="77582-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="77582-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="77582-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="77582-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-132">Named clients</span></span>

<span data-ttu-id="77582-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="77582-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="77582-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="77582-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="77582-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="77582-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="77582-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="77582-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="77582-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="77582-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="77582-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="77582-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="77582-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="77582-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="77582-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="77582-140">CreateClient</span></span>

<span data-ttu-id="77582-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="77582-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="77582-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="77582-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="77582-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="77582-143">The configuration action is called.</span></span>

<span data-ttu-id="77582-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="77582-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="77582-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="77582-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="77582-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="77582-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="77582-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-147">Typed clients</span></span>

<span data-ttu-id="77582-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-148">Typed clients:</span></span>

* <span data-ttu-id="77582-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="77582-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="77582-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="77582-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="77582-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="77582-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="77582-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="77582-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="77582-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="77582-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="77582-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="77582-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="77582-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="77582-156">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="77582-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="77582-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="77582-157">In the preceding code:</span></span>

* <span data-ttu-id="77582-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="77582-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="77582-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="77582-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="77582-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="77582-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="77582-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="77582-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="77582-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="77582-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="77582-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="77582-164">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="77582-165">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="77582-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="77582-166">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="77582-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="77582-167">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="77582-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="77582-168">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="77582-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="77582-169">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="77582-170">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-170">Generated clients</span></span>

<span data-ttu-id="77582-171">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="77582-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="77582-172">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="77582-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="77582-173">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="77582-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="77582-174">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="77582-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="77582-175">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="77582-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="77582-176">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="77582-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="77582-177">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="77582-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="77582-178">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="77582-178">Outgoing request middleware</span></span>

<span data-ttu-id="77582-179">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="77582-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="77582-180">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="77582-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="77582-181">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="77582-182">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="77582-183">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="77582-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="77582-184">此模式：</span><span class="sxs-lookup"><span data-stu-id="77582-184">This pattern:</span></span>

  * <span data-ttu-id="77582-185">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="77582-186">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="77582-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="77582-187">缓存</span><span class="sxs-lookup"><span data-stu-id="77582-187">caching</span></span>
    * <span data-ttu-id="77582-188">错误处理</span><span class="sxs-lookup"><span data-stu-id="77582-188">error handling</span></span>
    * <span data-ttu-id="77582-189">序列化</span><span class="sxs-lookup"><span data-stu-id="77582-189">serialization</span></span>
    * <span data-ttu-id="77582-190">logging</span><span class="sxs-lookup"><span data-stu-id="77582-190">logging</span></span>

<span data-ttu-id="77582-191">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="77582-191">To create a delegating handler:</span></span>

* <span data-ttu-id="77582-192">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="77582-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="77582-193">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="77582-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="77582-194">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="77582-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="77582-195">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="77582-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="77582-196">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="77582-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="77582-197">可使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="77582-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="77582-198">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="77582-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="77582-199">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="77582-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="77582-200">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="77582-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="77582-201">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="77582-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="77582-202">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="77582-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="77582-203">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="77582-204">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="77582-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="77582-205">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="77582-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="77582-206">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="77582-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="77582-207">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="77582-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="77582-208">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="77582-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="77582-209">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-209">Use Polly-based handlers</span></span>

<span data-ttu-id="77582-210">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="77582-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="77582-211">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="77582-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="77582-212">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="77582-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="77582-213">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="77582-214">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="77582-215">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="77582-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="77582-216">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="77582-216">Handle transient faults</span></span>

<span data-ttu-id="77582-217">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="77582-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="77582-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="77582-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="77582-219">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="77582-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="77582-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="77582-220">HTTP 5xx</span></span>
* <span data-ttu-id="77582-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="77582-221">HTTP 408</span></span>

<span data-ttu-id="77582-222">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="77582-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="77582-223">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="77582-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="77582-224">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="77582-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="77582-225">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="77582-225">Dynamically select policies</span></span>

<span data-ttu-id="77582-226">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="77582-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="77582-227">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="77582-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="77582-228">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="77582-229">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="77582-230">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="77582-231">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="77582-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="77582-232">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="77582-232">In the preceding example:</span></span>

* <span data-ttu-id="77582-233">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-233">Two handlers are added.</span></span>
* <span data-ttu-id="77582-234">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="77582-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="77582-235">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="77582-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="77582-236">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="77582-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="77582-237">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="77582-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="77582-238">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="77582-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="77582-239">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="77582-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="77582-240">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="77582-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="77582-241">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="77582-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="77582-242">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="77582-242">In the following code:</span></span>

* <span data-ttu-id="77582-243">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="77582-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="77582-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="77582-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="77582-245">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="77582-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="77582-246">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="77582-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="77582-247">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-248">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="77582-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="77582-249">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="77582-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="77582-250">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="77582-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="77582-251">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="77582-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="77582-252">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="77582-253">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="77582-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="77582-254">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="77582-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="77582-255">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="77582-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="77582-256">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="77582-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="77582-257">`HttpClient` 实例通常可视为无需处置的 .NET 对象  。</span><span class="sxs-lookup"><span data-stu-id="77582-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="77582-258">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="77582-259">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="77582-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="77582-260">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="77582-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-261">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="77582-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="77582-262">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="77582-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="77582-263">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="77582-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="77582-264">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="77582-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="77582-265">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="77582-266">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="77582-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="77582-267">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="77582-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="77582-268">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="77582-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="77582-269">根据需要，使用 `new HttpClient(handler, dispostHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-269">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="77582-270">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="77582-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="77582-271">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="77582-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="77582-272">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="77582-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="77582-273">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="77582-274">Cookie</span><span class="sxs-lookup"><span data-stu-id="77582-274">Cookies</span></span>

<span data-ttu-id="77582-275">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="77582-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="77582-276">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="77582-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="77582-277">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="77582-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="77582-278">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="77582-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="77582-279">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="77582-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="77582-280">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="77582-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="77582-281">Logging</span><span class="sxs-lookup"><span data-stu-id="77582-281">Logging</span></span>

<span data-ttu-id="77582-282">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="77582-283">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="77582-284">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="77582-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="77582-285">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="77582-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="77582-286">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息   。</span><span class="sxs-lookup"><span data-stu-id="77582-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="77582-287">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="77582-288">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="77582-289">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="77582-290">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="77582-291">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”   。</span><span class="sxs-lookup"><span data-stu-id="77582-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="77582-292">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="77582-293">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="77582-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="77582-294">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="77582-295">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="77582-296">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="77582-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="77582-297">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="77582-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="77582-298">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="77582-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="77582-299">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="77582-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="77582-300"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="77582-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="77582-301">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="77582-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="77582-302">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="77582-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="77582-303">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="77582-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="77582-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="77582-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="77582-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="77582-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="77582-306">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="77582-306">In the following example:</span></span>

* <span data-ttu-id="77582-307"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="77582-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="77582-308">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="77582-309">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="77582-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="77582-310">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="77582-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="77582-311">其他资源</span><span class="sxs-lookup"><span data-stu-id="77582-311">Additional resources</span></span>

* [<span data-ttu-id="77582-312">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="77582-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="77582-313">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="77582-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="77582-314">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="77582-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="77582-315">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="77582-315">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="77582-316">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-316">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="77582-317">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="77582-317">It offers the following benefits:</span></span>

* <span data-ttu-id="77582-318">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-318">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="77582-319">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="77582-319">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="77582-320">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="77582-320">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="77582-321">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="77582-321">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="77582-322">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="77582-322">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="77582-323">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="77582-323">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="77582-324">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="77582-324">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="77582-325">消耗模式</span><span class="sxs-lookup"><span data-stu-id="77582-325">Consumption patterns</span></span>

<span data-ttu-id="77582-326">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="77582-326">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="77582-327">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-327">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="77582-328">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-328">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="77582-329">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-329">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="77582-330">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-330">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="77582-331">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="77582-331">None of them are strictly superior to another.</span></span> <span data-ttu-id="77582-332">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="77582-332">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="77582-333">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-333">Basic usage</span></span>

<span data-ttu-id="77582-334">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="77582-334">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="77582-335">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="77582-335">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="77582-336">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="77582-336">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="77582-337">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="77582-337">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="77582-338">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="77582-338">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="77582-339">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="77582-339">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="77582-340">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-340">Named clients</span></span>

<span data-ttu-id="77582-341">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="77582-341">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="77582-342">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="77582-342">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="77582-343">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="77582-343">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="77582-344">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="77582-344">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="77582-345">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="77582-345">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="77582-346">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-346">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="77582-347">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="77582-347">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="77582-348">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="77582-348">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="77582-349">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="77582-349">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="77582-350">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-350">Typed clients</span></span>

<span data-ttu-id="77582-351">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-351">Typed clients:</span></span>

* <span data-ttu-id="77582-352">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="77582-352">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="77582-353">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="77582-353">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="77582-354">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="77582-354">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="77582-355">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="77582-355">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="77582-356">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="77582-356">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="77582-357">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="77582-357">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="77582-358">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="77582-358">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="77582-359">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="77582-359">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="77582-360">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="77582-360">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="77582-361">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="77582-361">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="77582-362">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="77582-362">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="77582-363">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-363">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="77582-364">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-364">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="77582-365">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="77582-365">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="77582-366">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="77582-366">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="77582-367">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-367">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="77582-368">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="77582-368">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="77582-369">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="77582-369">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="77582-370">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-370">Generated clients</span></span>

<span data-ttu-id="77582-371">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="77582-371">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="77582-372">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="77582-372">Refit is a REST library for .NET.</span></span> <span data-ttu-id="77582-373">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="77582-373">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="77582-374">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="77582-374">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="77582-375">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="77582-375">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="77582-376">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="77582-376">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="77582-377">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="77582-377">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="77582-378">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="77582-378">Outgoing request middleware</span></span>

<span data-ttu-id="77582-379">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="77582-379">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="77582-380">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-380">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="77582-381">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-381">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="77582-382">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="77582-382">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="77582-383">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-383">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="77582-384">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="77582-384">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="77582-385">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="77582-385">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="77582-386">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="77582-386">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="77582-387">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-387">The preceding code defines a basic handler.</span></span> <span data-ttu-id="77582-388">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="77582-388">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="77582-389">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="77582-389">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="77582-390">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="77582-390">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="77582-391">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="77582-391">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="77582-392">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="77582-392">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="77582-393">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="77582-393">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="77582-394">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="77582-394">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="77582-395">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="77582-395">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="77582-396">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="77582-396">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="77582-397">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-397">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="77582-398">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="77582-398">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="77582-399">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="77582-399">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="77582-400">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-400">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="77582-401">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="77582-401">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="77582-402">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="77582-402">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="77582-403">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-403">Use Polly-based handlers</span></span>

<span data-ttu-id="77582-404">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="77582-404">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="77582-405">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="77582-405">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="77582-406">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="77582-406">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="77582-407">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-407">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="77582-408">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="77582-408">The Polly extensions:</span></span>

* <span data-ttu-id="77582-409">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-409">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="77582-410">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="77582-410">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="77582-411">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="77582-411">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="77582-412">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="77582-412">Handle transient faults</span></span>

<span data-ttu-id="77582-413">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="77582-413">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="77582-414">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="77582-414">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="77582-415">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="77582-415">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="77582-416">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="77582-416">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="77582-417">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="77582-417">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="77582-418">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="77582-418">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="77582-419">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="77582-419">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="77582-420">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="77582-420">Dynamically select policies</span></span>

<span data-ttu-id="77582-421">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-421">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="77582-422">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="77582-422">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="77582-423">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="77582-423">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="77582-424">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-424">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="77582-425">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-425">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="77582-426">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-426">Add multiple Polly handlers</span></span>

<span data-ttu-id="77582-427">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="77582-427">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="77582-428">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-428">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="77582-429">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="77582-429">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="77582-430">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="77582-430">Failed requests are retried up to three times.</span></span> <span data-ttu-id="77582-431">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="77582-431">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="77582-432">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="77582-432">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="77582-433">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="77582-433">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="77582-434">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="77582-434">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="77582-435">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="77582-435">Add policies from the Polly registry</span></span>

<span data-ttu-id="77582-436">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="77582-436">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="77582-437">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="77582-437">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="77582-438">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="77582-438">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="77582-439">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="77582-439">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="77582-440">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="77582-440">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="77582-441">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="77582-441">HttpClient and lifetime management</span></span>

<span data-ttu-id="77582-442">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-442">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-443">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="77582-443">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="77582-444">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="77582-444">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="77582-445">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="77582-445">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="77582-446">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="77582-446">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="77582-447">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-447">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="77582-448">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="77582-448">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="77582-449">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="77582-449">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="77582-450">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="77582-450">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="77582-451">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="77582-451">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="77582-452">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="77582-452">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="77582-453">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-453">Disposal of the client isn't required.</span></span> <span data-ttu-id="77582-454">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-454">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="77582-455">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="77582-455">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="77582-456">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="77582-456">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="77582-457">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="77582-457">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-458">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="77582-458">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="77582-459">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="77582-459">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="77582-460">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="77582-460">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="77582-461">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="77582-461">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="77582-462">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-462">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="77582-463">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="77582-463">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="77582-464">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="77582-464">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="77582-465">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="77582-465">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="77582-466">根据需要，使用 `new HttpClient(handler, dispostHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-466">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="77582-467">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="77582-467">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="77582-468">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="77582-468">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="77582-469">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="77582-469">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="77582-470">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-470">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="77582-471">Cookie</span><span class="sxs-lookup"><span data-stu-id="77582-471">Cookies</span></span>

<span data-ttu-id="77582-472">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="77582-472">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="77582-473">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="77582-473">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="77582-474">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="77582-474">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="77582-475">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="77582-475">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="77582-476">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="77582-476">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="77582-477">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="77582-477">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="77582-478">Logging</span><span class="sxs-lookup"><span data-stu-id="77582-478">Logging</span></span>

<span data-ttu-id="77582-479">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-479">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="77582-480">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-480">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="77582-481">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="77582-481">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="77582-482">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="77582-482">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="77582-483">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-483">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="77582-484">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-484">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="77582-485">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-485">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="77582-486">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-486">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="77582-487">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-487">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="77582-488">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="77582-488">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="77582-489">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-489">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="77582-490">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="77582-490">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="77582-491">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-491">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="77582-492">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-492">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="77582-493">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="77582-493">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="77582-494">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="77582-494">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="77582-495">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="77582-495">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="77582-496">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="77582-496">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="77582-497"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="77582-497">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="77582-498">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="77582-498">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="77582-499">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="77582-499">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="77582-500">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="77582-500">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="77582-501">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="77582-501">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="77582-502">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="77582-502">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="77582-503">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="77582-503">In the following example:</span></span>

* <span data-ttu-id="77582-504"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="77582-504"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="77582-505">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-505">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="77582-506">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="77582-506">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="77582-507">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="77582-507">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="77582-508">其他资源</span><span class="sxs-lookup"><span data-stu-id="77582-508">Additional resources</span></span>

* [<span data-ttu-id="77582-509">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="77582-509">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="77582-510">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="77582-510">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="77582-511">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="77582-511">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="77582-512">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="77582-512">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="77582-513">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-513">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="77582-514">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="77582-514">It offers the following benefits:</span></span>

* <span data-ttu-id="77582-515">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-515">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="77582-516">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="77582-516">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="77582-517">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="77582-517">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="77582-518">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="77582-518">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="77582-519">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="77582-519">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="77582-520">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="77582-520">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="77582-521">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="77582-521">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77582-522">系统必备</span><span class="sxs-lookup"><span data-stu-id="77582-522">Prerequisites</span></span>

<span data-ttu-id="77582-523">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="77582-523">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="77582-524">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="77582-524">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="77582-525">消耗模式</span><span class="sxs-lookup"><span data-stu-id="77582-525">Consumption patterns</span></span>

<span data-ttu-id="77582-526">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="77582-526">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="77582-527">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-527">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="77582-528">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-528">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="77582-529">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-529">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="77582-530">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-530">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="77582-531">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="77582-531">None of them are strictly superior to another.</span></span> <span data-ttu-id="77582-532">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="77582-532">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="77582-533">基本用法</span><span class="sxs-lookup"><span data-stu-id="77582-533">Basic usage</span></span>

<span data-ttu-id="77582-534">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="77582-534">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="77582-535">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="77582-535">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="77582-536">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="77582-536">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="77582-537">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="77582-537">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="77582-538">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="77582-538">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="77582-539">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="77582-539">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="77582-540">命名客户端</span><span class="sxs-lookup"><span data-stu-id="77582-540">Named clients</span></span>

<span data-ttu-id="77582-541">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="77582-541">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="77582-542">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="77582-542">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="77582-543">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="77582-543">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="77582-544">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="77582-544">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="77582-545">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="77582-545">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="77582-546">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-546">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="77582-547">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="77582-547">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="77582-548">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="77582-548">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="77582-549">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="77582-549">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="77582-550">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="77582-550">Typed clients</span></span>

<span data-ttu-id="77582-551">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-551">Typed clients:</span></span>

* <span data-ttu-id="77582-552">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="77582-552">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="77582-553">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="77582-553">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="77582-554">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="77582-554">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="77582-555">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="77582-555">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="77582-556">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="77582-556">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="77582-557">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="77582-557">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="77582-558">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="77582-558">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="77582-559">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="77582-559">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="77582-560">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="77582-560">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="77582-561">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="77582-561">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="77582-562">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="77582-562">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="77582-563">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-563">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="77582-564">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="77582-564">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="77582-565">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="77582-565">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="77582-566">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="77582-566">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="77582-567">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-567">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="77582-568">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="77582-568">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="77582-569">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="77582-569">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="77582-570">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="77582-570">Generated clients</span></span>

<span data-ttu-id="77582-571">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="77582-571">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="77582-572">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="77582-572">Refit is a REST library for .NET.</span></span> <span data-ttu-id="77582-573">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="77582-573">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="77582-574">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="77582-574">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="77582-575">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="77582-575">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="77582-576">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="77582-576">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="77582-577">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="77582-577">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="77582-578">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="77582-578">Outgoing request middleware</span></span>

<span data-ttu-id="77582-579">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="77582-579">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="77582-580">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-580">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="77582-581">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-581">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="77582-582">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="77582-582">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="77582-583">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="77582-583">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="77582-584">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="77582-584">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="77582-585">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="77582-585">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="77582-586">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="77582-586">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="77582-587">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-587">The preceding code defines a basic handler.</span></span> <span data-ttu-id="77582-588">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="77582-588">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="77582-589">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="77582-589">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="77582-590">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="77582-590">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="77582-591">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="77582-591">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="77582-592">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="77582-592">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="77582-593">处理程序必须  在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="77582-593">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="77582-594">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="77582-594">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="77582-595">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="77582-595">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="77582-596">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="77582-596">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="77582-597">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="77582-597">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="77582-598">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-598">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="77582-599">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="77582-599">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="77582-600">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="77582-600">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="77582-601">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-601">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="77582-602">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="77582-602">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="77582-603">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="77582-603">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="77582-604">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-604">Use Polly-based handlers</span></span>

<span data-ttu-id="77582-605">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="77582-605">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="77582-606">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="77582-606">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="77582-607">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="77582-607">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="77582-608">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-608">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="77582-609">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="77582-609">The Polly extensions:</span></span>

* <span data-ttu-id="77582-610">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-610">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="77582-611">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="77582-611">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="77582-612">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="77582-612">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="77582-613">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="77582-613">Handle transient faults</span></span>

<span data-ttu-id="77582-614">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="77582-614">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="77582-615">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="77582-615">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="77582-616">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="77582-616">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="77582-617">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="77582-617">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="77582-618">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="77582-618">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="77582-619">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="77582-619">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="77582-620">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="77582-620">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="77582-621">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="77582-621">Dynamically select policies</span></span>

<span data-ttu-id="77582-622">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-622">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="77582-623">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="77582-623">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="77582-624">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="77582-624">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="77582-625">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-625">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="77582-626">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="77582-626">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="77582-627">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="77582-627">Add multiple Polly handlers</span></span>

<span data-ttu-id="77582-628">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="77582-628">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="77582-629">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-629">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="77582-630">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="77582-630">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="77582-631">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="77582-631">Failed requests are retried up to three times.</span></span> <span data-ttu-id="77582-632">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="77582-632">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="77582-633">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="77582-633">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="77582-634">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="77582-634">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="77582-635">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="77582-635">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="77582-636">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="77582-636">Add policies from the Polly registry</span></span>

<span data-ttu-id="77582-637">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="77582-637">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="77582-638">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="77582-638">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="77582-639">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="77582-639">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="77582-640">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="77582-640">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="77582-641">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="77582-641">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="77582-642">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="77582-642">HttpClient and lifetime management</span></span>

<span data-ttu-id="77582-643">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-643">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-644">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="77582-644">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="77582-645">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="77582-645">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="77582-646">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="77582-646">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="77582-647">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="77582-647">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="77582-648">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="77582-648">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="77582-649">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="77582-649">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="77582-650">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="77582-650">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="77582-651">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="77582-651">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="77582-652">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="77582-652">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="77582-653">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="77582-653">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="77582-654">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="77582-654">Disposal of the client isn't required.</span></span> <span data-ttu-id="77582-655">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-655">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="77582-656">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="77582-656">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="77582-657">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="77582-657">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="77582-658">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="77582-658">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="77582-659">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="77582-659">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="77582-660">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="77582-660">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="77582-661">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="77582-661">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="77582-662">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="77582-662">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="77582-663">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-663">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="77582-664">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="77582-664">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="77582-665">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="77582-665">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="77582-666">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="77582-666">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="77582-667">根据需要，使用 `new HttpClient(handler, dispostHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="77582-667">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="77582-668">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="77582-668">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="77582-669">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="77582-669">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="77582-670">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="77582-670">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="77582-671">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="77582-671">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="77582-672">Cookie</span><span class="sxs-lookup"><span data-stu-id="77582-672">Cookies</span></span>

<span data-ttu-id="77582-673">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="77582-673">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="77582-674">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="77582-674">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="77582-675">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="77582-675">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="77582-676">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="77582-676">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="77582-677">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="77582-677">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="77582-678">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="77582-678">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="77582-679">Logging</span><span class="sxs-lookup"><span data-stu-id="77582-679">Logging</span></span>

<span data-ttu-id="77582-680">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-680">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="77582-681">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="77582-681">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="77582-682">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="77582-682">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="77582-683">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="77582-683">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="77582-684">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-684">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="77582-685">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-685">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="77582-686">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-686">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="77582-687">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-687">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="77582-688">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="77582-688">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="77582-689">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="77582-689">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="77582-690">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="77582-690">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="77582-691">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="77582-691">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="77582-692">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-692">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="77582-693">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="77582-693">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="77582-694">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="77582-694">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="77582-695">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="77582-695">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="77582-696">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="77582-696">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="77582-697">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="77582-697">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="77582-698"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="77582-698">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="77582-699">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="77582-699">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="77582-700">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="77582-700">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="77582-701">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="77582-701">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="77582-702">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="77582-702">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="77582-703">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="77582-703">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="77582-704">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="77582-704">In the following example:</span></span>

* <span data-ttu-id="77582-705"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="77582-705"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="77582-706">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="77582-706">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="77582-707">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="77582-707">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="77582-708">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="77582-708">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="77582-709">其他资源</span><span class="sxs-lookup"><span data-stu-id="77582-709">Additional resources</span></span>

* [<span data-ttu-id="77582-710">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="77582-710">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="77582-711">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="77582-711">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="77582-712">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="77582-712">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
