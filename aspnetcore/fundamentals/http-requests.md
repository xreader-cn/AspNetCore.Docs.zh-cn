---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 9b9da82191a587be0603ee114562e9a964f05250
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870393"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="bd966-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="bd966-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd966-104">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="bd966-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="bd966-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bd966-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="bd966-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="bd966-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bd966-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="bd966-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bd966-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="bd966-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="bd966-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="bd966-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="bd966-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="bd966-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="bd966-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="bd966-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bd966-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bd966-115">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="bd966-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="bd966-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="bd966-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="bd966-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="bd966-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bd966-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="bd966-118">Consumption patterns</span></span>

<span data-ttu-id="bd966-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="bd966-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bd966-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bd966-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bd966-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bd966-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bd966-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="bd966-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bd966-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-125">Basic usage</span></span>

<span data-ttu-id="bd966-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="bd966-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bd966-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="bd966-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bd966-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="bd966-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bd966-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="bd966-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="bd966-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="bd966-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="bd966-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="bd966-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bd966-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-132">Named clients</span></span>

<span data-ttu-id="bd966-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="bd966-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="bd966-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="bd966-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="bd966-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="bd966-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="bd966-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="bd966-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bd966-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="bd966-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="bd966-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="bd966-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="bd966-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="bd966-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="bd966-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="bd966-140">CreateClient</span></span>

<span data-ttu-id="bd966-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="bd966-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="bd966-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="bd966-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="bd966-143">The configuration action is called.</span></span>

<span data-ttu-id="bd966-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="bd966-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bd966-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="bd966-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bd966-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="bd966-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bd966-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-147">Typed clients</span></span>

<span data-ttu-id="bd966-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-148">Typed clients:</span></span>

* <span data-ttu-id="bd966-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="bd966-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bd966-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="bd966-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bd966-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="bd966-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bd966-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="bd966-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="bd966-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="bd966-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="bd966-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="bd966-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="bd966-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="bd966-156">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="bd966-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bd966-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="bd966-157">In the preceding code:</span></span>

* <span data-ttu-id="bd966-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="bd966-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="bd966-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="bd966-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="bd966-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="bd966-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bd966-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="bd966-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="bd966-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bd966-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bd966-164">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bd966-165">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="bd966-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bd966-166">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="bd966-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="bd966-167">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="bd966-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bd966-168">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="bd966-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="bd966-169">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bd966-170">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-170">Generated clients</span></span>

<span data-ttu-id="bd966-171">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="bd966-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bd966-172">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="bd966-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bd966-173">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="bd966-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bd966-174">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="bd966-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bd966-175">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="bd966-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bd966-176">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bd966-177">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bd966-178">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="bd966-178">Outgoing request middleware</span></span>

<span data-ttu-id="bd966-179">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bd966-180">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="bd966-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="bd966-181">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="bd966-182">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bd966-183">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="bd966-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bd966-184">此模式：</span><span class="sxs-lookup"><span data-stu-id="bd966-184">This pattern:</span></span>

  * <span data-ttu-id="bd966-185">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="bd966-186">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="bd966-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="bd966-187">缓存</span><span class="sxs-lookup"><span data-stu-id="bd966-187">caching</span></span>
    * <span data-ttu-id="bd966-188">错误处理</span><span class="sxs-lookup"><span data-stu-id="bd966-188">error handling</span></span>
    * <span data-ttu-id="bd966-189">序列化</span><span class="sxs-lookup"><span data-stu-id="bd966-189">serialization</span></span>
    * <span data-ttu-id="bd966-190">日志记录</span><span class="sxs-lookup"><span data-stu-id="bd966-190">logging</span></span>

<span data-ttu-id="bd966-191">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="bd966-191">To create a delegating handler:</span></span>

* <span data-ttu-id="bd966-192">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="bd966-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="bd966-193">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="bd966-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="bd966-194">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="bd966-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bd966-195">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="bd966-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="bd966-196">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="bd966-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="bd966-197">可使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="bd966-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="bd966-198">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="bd966-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bd966-199">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="bd966-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="bd966-200">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="bd966-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="bd966-201">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="bd966-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="bd966-202">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="bd966-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="bd966-203">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bd966-204">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="bd966-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bd966-205">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="bd966-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bd966-206">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="bd966-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="bd966-207">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="bd966-208">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="bd966-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bd966-209">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-209">Use Polly-based handlers</span></span>

<span data-ttu-id="bd966-210">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="bd966-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bd966-211">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="bd966-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bd966-212">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="bd966-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bd966-213">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bd966-214">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="bd966-215">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="bd966-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bd966-216">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="bd966-216">Handle transient faults</span></span>

<span data-ttu-id="bd966-217">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bd966-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="bd966-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bd966-219">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="bd966-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="bd966-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="bd966-220">HTTP 5xx</span></span>
* <span data-ttu-id="bd966-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="bd966-221">HTTP 408</span></span>

<span data-ttu-id="bd966-222">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="bd966-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="bd966-223">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bd966-224">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="bd966-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bd966-225">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="bd966-225">Dynamically select policies</span></span>

<span data-ttu-id="bd966-226">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="bd966-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="bd966-227">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="bd966-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bd966-228">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bd966-229">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bd966-230">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="bd966-231">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="bd966-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bd966-232">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="bd966-232">In the preceding example:</span></span>

* <span data-ttu-id="bd966-233">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-233">Two handlers are added.</span></span>
* <span data-ttu-id="bd966-234">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="bd966-235">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="bd966-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="bd966-236">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="bd966-237">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="bd966-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="bd966-238">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bd966-239">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bd966-240">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="bd966-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="bd966-241">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="bd966-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="bd966-242">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="bd966-242">In the following code:</span></span>

* <span data-ttu-id="bd966-243">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="bd966-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="bd966-245">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="bd966-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bd966-246">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="bd966-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="bd966-247">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-248">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="bd966-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="bd966-249">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="bd966-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bd966-250">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="bd966-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bd966-251">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="bd966-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bd966-252">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bd966-253">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="bd966-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bd966-254">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="bd966-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="bd966-255">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="bd966-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bd966-256">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="bd966-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="bd966-257">`HttpClient` 实例通常可视为无需处置的 .NET 对象  。</span><span class="sxs-lookup"><span data-stu-id="bd966-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="bd966-258">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bd966-259">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="bd966-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="bd966-260">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-261">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="bd966-262">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="bd966-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="bd966-263">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="bd966-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="bd966-264">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="bd966-265">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="bd966-266">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="bd966-267">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="bd966-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="bd966-268">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="bd966-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="bd966-269">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-269">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="bd966-270">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="bd966-271">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="bd966-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="bd966-272">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="bd966-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="bd966-273">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="bd966-274">Cookie</span><span class="sxs-lookup"><span data-stu-id="bd966-274">Cookies</span></span>

<span data-ttu-id="bd966-275">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="bd966-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="bd966-276">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="bd966-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="bd966-277">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="bd966-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="bd966-278">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="bd966-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="bd966-279">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="bd966-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="bd966-280">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="bd966-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="bd966-281">Logging</span><span class="sxs-lookup"><span data-stu-id="bd966-281">Logging</span></span>

<span data-ttu-id="bd966-282">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bd966-283">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="bd966-284">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="bd966-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bd966-285">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="bd966-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bd966-286">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息   。</span><span class="sxs-lookup"><span data-stu-id="bd966-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="bd966-287">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bd966-288">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bd966-289">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bd966-290">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bd966-291">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”   。</span><span class="sxs-lookup"><span data-stu-id="bd966-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="bd966-292">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="bd966-293">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bd966-294">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bd966-295">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="bd966-296">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="bd966-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bd966-297">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bd966-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bd966-298">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="bd966-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bd966-299">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="bd966-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bd966-300"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="bd966-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bd966-301">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="bd966-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bd966-302">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="bd966-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bd966-303">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="bd966-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bd966-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="bd966-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bd966-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="bd966-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bd966-306">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="bd966-306">In the following example:</span></span>

* <span data-ttu-id="bd966-307"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="bd966-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bd966-308">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bd966-309">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="bd966-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bd966-310">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="bd966-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="bd966-311">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="bd966-311">Header propagation middleware</span></span>

<span data-ttu-id="bd966-312">标头传播是一个 ASP.NET Core 中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-312">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="bd966-313">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="bd966-313">To use header propagation:</span></span>

* <span data-ttu-id="bd966-314">引用 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 包。</span><span class="sxs-lookup"><span data-stu-id="bd966-314">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="bd966-315">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="bd966-315">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="bd966-316">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="bd966-316">The client includes the configured headers on outbound requests:</span></span>

  ```C#
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="bd966-317">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd966-317">Additional resources</span></span>

* [<span data-ttu-id="bd966-318">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="bd966-318">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bd966-319">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="bd966-319">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bd966-320">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="bd966-320">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="bd966-321">如何在 .NET 中对 JSON 数据进行序列化和反序列化</span><span class="sxs-lookup"><span data-stu-id="bd966-321">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bd966-322">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="bd966-322">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="bd966-323">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-323">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bd966-324">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="bd966-324">It offers the following benefits:</span></span>

* <span data-ttu-id="bd966-325">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-325">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bd966-326">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="bd966-326">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bd966-327">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="bd966-327">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="bd966-328">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="bd966-328">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="bd966-329">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-329">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bd966-330">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-330">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bd966-331">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bd966-331">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bd966-332">消耗模式</span><span class="sxs-lookup"><span data-stu-id="bd966-332">Consumption patterns</span></span>

<span data-ttu-id="bd966-333">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="bd966-333">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bd966-334">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-334">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bd966-335">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-335">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bd966-336">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-336">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bd966-337">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-337">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bd966-338">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="bd966-338">None of them are strictly superior to another.</span></span> <span data-ttu-id="bd966-339">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="bd966-339">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bd966-340">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-340">Basic usage</span></span>

<span data-ttu-id="bd966-341">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="bd966-341">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bd966-342">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="bd966-342">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bd966-343">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="bd966-343">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bd966-344">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="bd966-344">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="bd966-345">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="bd966-345">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="bd966-346">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="bd966-346">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bd966-347">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-347">Named clients</span></span>

<span data-ttu-id="bd966-348">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="bd966-348">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="bd966-349">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="bd966-349">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bd966-350">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="bd966-350">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="bd966-351">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="bd966-351">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="bd966-352">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="bd966-352">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="bd966-353">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-353">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="bd966-354">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="bd966-354">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bd966-355">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="bd966-355">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bd966-356">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="bd966-356">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bd966-357">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-357">Typed clients</span></span>

<span data-ttu-id="bd966-358">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-358">Typed clients:</span></span>

* <span data-ttu-id="bd966-359">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="bd966-359">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bd966-360">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="bd966-360">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bd966-361">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="bd966-361">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bd966-362">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="bd966-362">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="bd966-363">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="bd966-363">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="bd966-364">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="bd966-364">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bd966-365">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="bd966-365">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="bd966-366">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="bd966-366">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="bd966-367">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="bd966-367">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bd966-368">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="bd966-368">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="bd966-369">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="bd966-369">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bd966-370">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-370">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bd966-371">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-371">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bd966-372">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="bd966-372">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bd966-373">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="bd966-373">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="bd966-374">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-374">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bd966-375">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="bd966-375">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="bd966-376">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="bd966-376">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bd966-377">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-377">Generated clients</span></span>

<span data-ttu-id="bd966-378">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="bd966-378">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bd966-379">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="bd966-379">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bd966-380">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="bd966-380">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bd966-381">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="bd966-381">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bd966-382">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="bd966-382">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bd966-383">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-383">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bd966-384">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-384">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bd966-385">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="bd966-385">Outgoing request middleware</span></span>

<span data-ttu-id="bd966-386">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-386">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bd966-387">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-387">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="bd966-388">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-388">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bd966-389">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="bd966-389">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bd966-390">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-390">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="bd966-391">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="bd966-391">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="bd966-392">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="bd966-392">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="bd966-393">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="bd966-393">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bd966-394">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-394">The preceding code defines a basic handler.</span></span> <span data-ttu-id="bd966-395">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="bd966-395">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="bd966-396">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="bd966-396">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="bd966-397">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="bd966-397">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="bd966-398">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="bd966-398">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="bd966-399">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="bd966-399">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bd966-400">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="bd966-400">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="bd966-401">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="bd966-401">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="bd966-402">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="bd966-402">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="bd966-403">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="bd966-403">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="bd966-404">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-404">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bd966-405">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="bd966-405">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bd966-406">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="bd966-406">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bd966-407">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-407">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="bd966-408">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-408">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="bd966-409">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="bd966-409">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bd966-410">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-410">Use Polly-based handlers</span></span>

<span data-ttu-id="bd966-411">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="bd966-411">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bd966-412">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="bd966-412">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bd966-413">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="bd966-413">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bd966-414">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-414">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bd966-415">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="bd966-415">The Polly extensions:</span></span>

* <span data-ttu-id="bd966-416">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-416">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="bd966-417">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="bd966-417">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="bd966-418">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="bd966-418">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bd966-419">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="bd966-419">Handle transient faults</span></span>

<span data-ttu-id="bd966-420">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-420">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bd966-421">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="bd966-421">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bd966-422">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="bd966-422">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="bd966-423">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="bd966-423">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bd966-424">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="bd966-424">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="bd966-425">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-425">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bd966-426">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="bd966-426">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bd966-427">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="bd966-427">Dynamically select policies</span></span>

<span data-ttu-id="bd966-428">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-428">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="bd966-429">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="bd966-429">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="bd966-430">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="bd966-430">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bd966-431">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-431">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bd966-432">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-432">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bd966-433">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-433">Add multiple Polly handlers</span></span>

<span data-ttu-id="bd966-434">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="bd966-434">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bd966-435">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-435">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="bd966-436">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-436">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="bd966-437">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="bd966-437">Failed requests are retried up to three times.</span></span> <span data-ttu-id="bd966-438">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-438">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="bd966-439">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="bd966-439">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="bd966-440">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-440">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bd966-441">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-441">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bd966-442">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="bd966-442">Add policies from the Polly registry</span></span>

<span data-ttu-id="bd966-443">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="bd966-443">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="bd966-444">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="bd966-444">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="bd966-445">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="bd966-445">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="bd966-446">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="bd966-446">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="bd966-447">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="bd966-447">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bd966-448">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="bd966-448">HttpClient and lifetime management</span></span>

<span data-ttu-id="bd966-449">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-449">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-450">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="bd966-450">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="bd966-451">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="bd966-451">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bd966-452">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="bd966-452">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bd966-453">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="bd966-453">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bd966-454">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-454">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bd966-455">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="bd966-455">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bd966-456">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="bd966-456">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="bd966-457">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="bd966-457">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bd966-458">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="bd966-458">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="bd966-459">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="bd966-459">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="bd966-460">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-460">Disposal of the client isn't required.</span></span> <span data-ttu-id="bd966-461">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-461">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bd966-462">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="bd966-462">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="bd966-463">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="bd966-463">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="bd966-464">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-464">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-465">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-465">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="bd966-466">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="bd966-466">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="bd966-467">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="bd966-467">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="bd966-468">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-468">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="bd966-469">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-469">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="bd966-470">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-470">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="bd966-471">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="bd966-471">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="bd966-472">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="bd966-472">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="bd966-473">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-473">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="bd966-474">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-474">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="bd966-475">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="bd966-475">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="bd966-476">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="bd966-476">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="bd966-477">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-477">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="bd966-478">Cookie</span><span class="sxs-lookup"><span data-stu-id="bd966-478">Cookies</span></span>

<span data-ttu-id="bd966-479">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="bd966-479">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="bd966-480">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="bd966-480">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="bd966-481">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="bd966-481">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="bd966-482">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="bd966-482">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="bd966-483">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="bd966-483">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="bd966-484">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="bd966-484">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="bd966-485">Logging</span><span class="sxs-lookup"><span data-stu-id="bd966-485">Logging</span></span>

<span data-ttu-id="bd966-486">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-486">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bd966-487">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-487">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="bd966-488">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="bd966-488">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bd966-489">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="bd966-489">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bd966-490">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-490">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="bd966-491">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-491">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bd966-492">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-492">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bd966-493">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-493">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bd966-494">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-494">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bd966-495">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="bd966-495">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="bd966-496">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-496">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="bd966-497">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-497">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bd966-498">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-498">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bd966-499">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-499">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="bd966-500">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="bd966-500">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bd966-501">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bd966-501">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bd966-502">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="bd966-502">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bd966-503">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="bd966-503">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bd966-504"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="bd966-504">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bd966-505">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="bd966-505">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bd966-506">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="bd966-506">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bd966-507">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="bd966-507">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bd966-508">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="bd966-508">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bd966-509">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="bd966-509">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bd966-510">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="bd966-510">In the following example:</span></span>

* <span data-ttu-id="bd966-511"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="bd966-511"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bd966-512">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-512">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bd966-513">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="bd966-513">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bd966-514">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="bd966-514">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="bd966-515">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd966-515">Additional resources</span></span>

* [<span data-ttu-id="bd966-516">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="bd966-516">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bd966-517">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="bd966-517">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bd966-518">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="bd966-518">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="bd966-519">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="bd966-519">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="bd966-520">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-520">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bd966-521">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="bd966-521">It offers the following benefits:</span></span>

* <span data-ttu-id="bd966-522">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-522">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bd966-523">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="bd966-523">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bd966-524">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="bd966-524">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="bd966-525">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="bd966-525">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="bd966-526">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-526">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bd966-527">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-527">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bd966-528">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bd966-528">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd966-529">先决条件</span><span class="sxs-lookup"><span data-stu-id="bd966-529">Prerequisites</span></span>

<span data-ttu-id="bd966-530">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="bd966-530">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="bd966-531">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="bd966-531">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bd966-532">消耗模式</span><span class="sxs-lookup"><span data-stu-id="bd966-532">Consumption patterns</span></span>

<span data-ttu-id="bd966-533">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="bd966-533">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bd966-534">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-534">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bd966-535">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-535">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bd966-536">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-536">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bd966-537">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-537">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bd966-538">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="bd966-538">None of them are strictly superior to another.</span></span> <span data-ttu-id="bd966-539">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="bd966-539">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bd966-540">基本用法</span><span class="sxs-lookup"><span data-stu-id="bd966-540">Basic usage</span></span>

<span data-ttu-id="bd966-541">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="bd966-541">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bd966-542">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="bd966-542">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bd966-543">`IHttpClientFactory` 可用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="bd966-543">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bd966-544">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="bd966-544">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="bd966-545">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="bd966-545">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="bd966-546">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="bd966-546">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bd966-547">命名客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-547">Named clients</span></span>

<span data-ttu-id="bd966-548">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="bd966-548">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="bd966-549">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="bd966-549">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bd966-550">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="bd966-550">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="bd966-551">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="bd966-551">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="bd966-552">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="bd966-552">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="bd966-553">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-553">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="bd966-554">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="bd966-554">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bd966-555">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="bd966-555">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bd966-556">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="bd966-556">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bd966-557">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-557">Typed clients</span></span>

<span data-ttu-id="bd966-558">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-558">Typed clients:</span></span>

* <span data-ttu-id="bd966-559">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="bd966-559">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bd966-560">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="bd966-560">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bd966-561">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="bd966-561">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bd966-562">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="bd966-562">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="bd966-563">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="bd966-563">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="bd966-564">类型化客户端在构造函数中接受 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="bd966-564">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bd966-565">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="bd966-565">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="bd966-566">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="bd966-566">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="bd966-567">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="bd966-567">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bd966-568">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="bd966-568">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="bd966-569">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="bd966-569">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bd966-570">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-570">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bd966-571">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="bd966-571">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bd966-572">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="bd966-572">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bd966-573">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="bd966-573">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="bd966-574">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-574">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bd966-575">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="bd966-575">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="bd966-576">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="bd966-576">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bd966-577">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="bd966-577">Generated clients</span></span>

<span data-ttu-id="bd966-578">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="bd966-578">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bd966-579">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="bd966-579">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bd966-580">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="bd966-580">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bd966-581">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="bd966-581">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bd966-582">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="bd966-582">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bd966-583">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-583">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bd966-584">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="bd966-584">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bd966-585">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="bd966-585">Outgoing request middleware</span></span>

<span data-ttu-id="bd966-586">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-586">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bd966-587">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-587">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="bd966-588">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-588">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bd966-589">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="bd966-589">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bd966-590">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="bd966-590">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="bd966-591">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="bd966-591">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="bd966-592">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="bd966-592">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="bd966-593">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="bd966-593">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bd966-594">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-594">The preceding code defines a basic handler.</span></span> <span data-ttu-id="bd966-595">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="bd966-595">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="bd966-596">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="bd966-596">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="bd966-597">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置中。</span><span class="sxs-lookup"><span data-stu-id="bd966-597">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="bd966-598">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="bd966-598">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="bd966-599">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="bd966-599">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bd966-600">处理程序必须  在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="bd966-600">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="bd966-601">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="bd966-601">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="bd966-602">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="bd966-602">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="bd966-603">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="bd966-603">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="bd966-604">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="bd966-604">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="bd966-605">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-605">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bd966-606">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="bd966-606">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bd966-607">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="bd966-607">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bd966-608">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-608">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="bd966-609">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-609">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="bd966-610">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="bd966-610">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bd966-611">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-611">Use Polly-based handlers</span></span>

<span data-ttu-id="bd966-612">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="bd966-612">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bd966-613">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="bd966-613">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bd966-614">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="bd966-614">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bd966-615">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-615">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bd966-616">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="bd966-616">The Polly extensions:</span></span>

* <span data-ttu-id="bd966-617">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-617">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="bd966-618">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="bd966-618">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="bd966-619">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="bd966-619">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bd966-620">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="bd966-620">Handle transient faults</span></span>

<span data-ttu-id="bd966-621">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-621">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bd966-622">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="bd966-622">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bd966-623">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="bd966-623">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="bd966-624">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="bd966-624">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bd966-625">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="bd966-625">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="bd966-626">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-626">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bd966-627">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="bd966-627">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bd966-628">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="bd966-628">Dynamically select policies</span></span>

<span data-ttu-id="bd966-629">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-629">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="bd966-630">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="bd966-630">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="bd966-631">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="bd966-631">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bd966-632">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-632">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bd966-633">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="bd966-633">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bd966-634">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="bd966-634">Add multiple Polly handlers</span></span>

<span data-ttu-id="bd966-635">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="bd966-635">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bd966-636">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-636">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="bd966-637">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-637">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="bd966-638">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="bd966-638">Failed requests are retried up to three times.</span></span> <span data-ttu-id="bd966-639">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="bd966-639">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="bd966-640">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="bd966-640">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="bd966-641">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-641">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bd966-642">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-642">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bd966-643">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="bd966-643">Add policies from the Polly registry</span></span>

<span data-ttu-id="bd966-644">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="bd966-644">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="bd966-645">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="bd966-645">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="bd966-646">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="bd966-646">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="bd966-647">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="bd966-647">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="bd966-648">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="bd966-648">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bd966-649">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="bd966-649">HttpClient and lifetime management</span></span>

<span data-ttu-id="bd966-650">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-650">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-651">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="bd966-651">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="bd966-652">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="bd966-652">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bd966-653">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="bd966-653">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bd966-654">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="bd966-654">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bd966-655">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="bd966-655">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bd966-656">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="bd966-656">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bd966-657">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="bd966-657">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="bd966-658">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="bd966-658">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bd966-659">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="bd966-659">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="bd966-660">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="bd966-660">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="bd966-661">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="bd966-661">Disposal of the client isn't required.</span></span> <span data-ttu-id="bd966-662">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-662">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bd966-663">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="bd966-663">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="bd966-664">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="bd966-664">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="bd966-665">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-665">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bd966-666">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="bd966-666">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="bd966-667">IHttpClientFactory 的替代项</span><span class="sxs-lookup"><span data-stu-id="bd966-667">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="bd966-668">通过在启用了 DI 的应用中使用 `IHttpClientFactory`，可避免：</span><span class="sxs-lookup"><span data-stu-id="bd966-668">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="bd966-669">通过共用 `HttpMessageHandler` 实例，解决资源耗尽问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-669">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="bd966-670">通过定期循环 `HttpMessageHandler` 实例，解决 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-670">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="bd966-671">此外，还有其他方法使用生命周期长的 <xref:System.Net.Http.SocketsHttpHandler> 实例来解决上述问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-671">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="bd966-672">在应用启动时创建 `SocketsHttpHandler` 的实例，并在应用的整个生命周期中使用它。</span><span class="sxs-lookup"><span data-stu-id="bd966-672">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="bd966-673">根据 DNS 刷新时间，将 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> 配置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="bd966-673">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="bd966-674">根据需要，使用 `new HttpClient(handler, disposeHandler: false)` 创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="bd966-674">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="bd966-675">上述方法使用 `IHttpClientFactory` 解决问题的类似方式解决资源管理问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-675">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="bd966-676">`SocketsHttpHandler` 在 `HttpClient` 实例之间共享连接。</span><span class="sxs-lookup"><span data-stu-id="bd966-676">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="bd966-677">此共享可防止套接字耗尽。</span><span class="sxs-lookup"><span data-stu-id="bd966-677">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="bd966-678">`SocketsHttpHandler` 会根据 `PooledConnectionLifetime` 循环连接，避免出现 DNS 过时问题。</span><span class="sxs-lookup"><span data-stu-id="bd966-678">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="bd966-679">Cookie</span><span class="sxs-lookup"><span data-stu-id="bd966-679">Cookies</span></span>

<span data-ttu-id="bd966-680">共用 `HttpMessageHandler` 实例将导致共享 `CookieContainer` 对象。</span><span class="sxs-lookup"><span data-stu-id="bd966-680">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="bd966-681">意外的 `CookieContainer` 对象共享通常会导致错误的代码。</span><span class="sxs-lookup"><span data-stu-id="bd966-681">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="bd966-682">对于需要 Cookie 的应用，请考虑执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="bd966-682">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="bd966-683">禁用自动 Cookie 处理</span><span class="sxs-lookup"><span data-stu-id="bd966-683">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="bd966-684">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="bd966-684">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="bd966-685">调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以禁用自动 Cookie 处理：</span><span class="sxs-lookup"><span data-stu-id="bd966-685">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="bd966-686">Logging</span><span class="sxs-lookup"><span data-stu-id="bd966-686">Logging</span></span>

<span data-ttu-id="bd966-687">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-687">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bd966-688">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-688">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="bd966-689">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="bd966-689">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bd966-690">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="bd966-690">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bd966-691">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-691">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="bd966-692">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-692">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bd966-693">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-693">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bd966-694">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-694">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bd966-695">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="bd966-695">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bd966-696">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="bd966-696">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="bd966-697">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="bd966-697">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="bd966-698">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="bd966-698">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bd966-699">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-699">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bd966-700">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="bd966-700">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="bd966-701">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="bd966-701">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bd966-702">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bd966-702">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bd966-703">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="bd966-703">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bd966-704">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="bd966-704">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bd966-705"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="bd966-705">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bd966-706">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="bd966-706">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bd966-707">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="bd966-707">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bd966-708">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="bd966-708">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bd966-709">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="bd966-709">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bd966-710">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="bd966-710">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bd966-711">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="bd966-711">In the following example:</span></span>

* <span data-ttu-id="bd966-712"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="bd966-712"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bd966-713">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="bd966-713">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bd966-714">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="bd966-714">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bd966-715">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="bd966-715">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="bd966-716">标头传播中间件</span><span class="sxs-lookup"><span data-stu-id="bd966-716">Header propagation middleware</span></span>

<span data-ttu-id="bd966-717">标头传播是一个社区支持的中间件，可将 HTTP 标头从传入请求传播到传出 HTTP 客户端请求。</span><span class="sxs-lookup"><span data-stu-id="bd966-717">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="bd966-718">使用标头传播：</span><span class="sxs-lookup"><span data-stu-id="bd966-718">To use header propagation:</span></span>

* <span data-ttu-id="bd966-719">引用 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 包的社区支持的端口。</span><span class="sxs-lookup"><span data-stu-id="bd966-719">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="bd966-720">ASP.NET Core 3.1 及更高版本支持 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。</span><span class="sxs-lookup"><span data-stu-id="bd966-720">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="bd966-721">在 `Startup` 中配置中间件和 `HttpClient`：</span><span class="sxs-lookup"><span data-stu-id="bd966-721">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="bd966-722">客户端在出站请求中包含配置的标头：</span><span class="sxs-lookup"><span data-stu-id="bd966-722">The client includes the configured headers on outbound requests:</span></span>

  ```C#
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="bd966-723">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd966-723">Additional resources</span></span>

* [<span data-ttu-id="bd966-724">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="bd966-724">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bd966-725">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="bd966-725">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bd966-726">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="bd966-726">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
