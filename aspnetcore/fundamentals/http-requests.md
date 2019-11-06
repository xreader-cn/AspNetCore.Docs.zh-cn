---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: a963833acfa12889c8ae3dac443962682e1cb931
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190579"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="efee8-103">在 ASP.NET Core 中使用 IHttpClientFactory 发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="efee8-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="efee8-104">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="efee8-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="efee8-105">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="efee8-106">`IHttpClientFactory` 的优势如下：</span><span class="sxs-lookup"><span data-stu-id="efee8-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="efee8-107">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="efee8-108">例如，可注册和配置名为 github 的客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="efee8-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="efee8-109">可以注册一个默认客户端用于一般性访问。</span><span class="sxs-lookup"><span data-stu-id="efee8-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="efee8-110">通过 `HttpClient` 中的委托处理程序来编码出站中间件的概念。</span><span class="sxs-lookup"><span data-stu-id="efee8-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="efee8-111">提供基于 Polly 的中间件的扩展，以利用 `HttpClient` 中的委托处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="efee8-112">管理基础 `HttpClientMessageHandler` 实例的池和生存期。</span><span class="sxs-lookup"><span data-stu-id="efee8-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="efee8-113">自动管理可避免手动管理 `HttpClient` 生存期时出现的常见 DNS（域名系统）问题。</span><span class="sxs-lookup"><span data-stu-id="efee8-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="efee8-114">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="efee8-115">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="efee8-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="efee8-116">此主题版本中的示例代码使用 <xref:System.Text.Json> 来对 HTTP 响应中返回的 JSON 内容进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="efee8-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="efee8-117">对于使用 `Json.NET` 和 `ReadAsAsync<T>` 的示例，请使用版本选择器选择此主题的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="efee8-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="efee8-118">消耗模式</span><span class="sxs-lookup"><span data-stu-id="efee8-118">Consumption patterns</span></span>

<span data-ttu-id="efee8-119">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="efee8-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="efee8-120">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="efee8-121">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="efee8-122">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="efee8-123">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="efee8-124">最佳方法取决于应用要求。</span><span class="sxs-lookup"><span data-stu-id="efee8-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="efee8-125">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-125">Basic usage</span></span>

<span data-ttu-id="efee8-126">可以通过调用 `AddHttpClient` 来注册 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="efee8-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="efee8-127">可以使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection) 来请求 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="efee8-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="efee8-128">以下代码使用 `IHttpClientFactory` 来创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="efee8-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="efee8-129">像前面的示例一样，使用 `IHttpClientFactory` 是重构现有应用的好方法。</span><span class="sxs-lookup"><span data-stu-id="efee8-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="efee8-130">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="efee8-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="efee8-131">在现有应用中创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="efee8-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="efee8-132">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-132">Named clients</span></span>

<span data-ttu-id="efee8-133">在以下情况下，命名客户端是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="efee8-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="efee8-134">应用需要 `HttpClient` 的许多不同用法。</span><span class="sxs-lookup"><span data-stu-id="efee8-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="efee8-135">许多 `HttpClient` 具有不同的配置。</span><span class="sxs-lookup"><span data-stu-id="efee8-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="efee8-136">可以在 `Startup.ConfigureServices` 中注册时指定命名 `HttpClient` 的配置：</span><span class="sxs-lookup"><span data-stu-id="efee8-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="efee8-137">在上述代码中，客户端配置如下：</span><span class="sxs-lookup"><span data-stu-id="efee8-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="efee8-138">基址为 `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="efee8-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="efee8-139">使用 GitHub API 需要的两个标头。</span><span class="sxs-lookup"><span data-stu-id="efee8-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="efee8-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="efee8-140">CreateClient</span></span>

<span data-ttu-id="efee8-141">每次调用 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 时：</span><span class="sxs-lookup"><span data-stu-id="efee8-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="efee8-142">创建 `HttpClient` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="efee8-143">调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="efee8-143">The configuration action is called.</span></span>

<span data-ttu-id="efee8-144">要创建命名客户端，请将其名称传递到 `CreateClient` 中：</span><span class="sxs-lookup"><span data-stu-id="efee8-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="efee8-145">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="efee8-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="efee8-146">代码可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="efee8-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="efee8-147">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-147">Typed clients</span></span>

<span data-ttu-id="efee8-148">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-148">Typed clients:</span></span>

* <span data-ttu-id="efee8-149">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="efee8-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="efee8-150">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="efee8-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="efee8-151">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="efee8-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="efee8-152">例如，可以使用单个类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="efee8-153">对于单个后端终结点。</span><span class="sxs-lookup"><span data-stu-id="efee8-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="efee8-154">封装处理终结点的所有逻辑。</span><span class="sxs-lookup"><span data-stu-id="efee8-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="efee8-155">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="efee8-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="efee8-156">类型化客户端在构造函数中接收 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="efee8-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="efee8-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="efee8-157">In the preceding code:</span></span>

* <span data-ttu-id="efee8-158">配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="efee8-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="efee8-159">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="efee8-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="efee8-160">可以创建特定于 API 的方法来公开 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="efee8-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="efee8-161">例如，创建 `GetAspNetDocsIssues` 方法来封装代码以检索未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="efee8-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="efee8-162">以下代码调用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 来注册类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="efee8-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="efee8-163">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="efee8-164">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="efee8-165">可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="efee8-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="efee8-166">可以将 `HttpClient` 封装在类型化客户端中，</span><span class="sxs-lookup"><span data-stu-id="efee8-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="efee8-167">定义一个在内部调用 `HttpClient` 实例的方法，而不是将其公开为属性：</span><span class="sxs-lookup"><span data-stu-id="efee8-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="efee8-168">在上述代码中，`HttpClient` 存储在私有字段中。</span><span class="sxs-lookup"><span data-stu-id="efee8-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="efee8-169">通过公共 `GetRepos` 方法访问 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="efee8-170">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-170">Generated clients</span></span>

<span data-ttu-id="efee8-171">`IHttpClientFactory` 可结合第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="efee8-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="efee8-172">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="efee8-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="efee8-173">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="efee8-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="efee8-174">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="efee8-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="efee8-175">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="efee8-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="efee8-176">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="efee8-177">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="efee8-178">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="efee8-178">Outgoing request middleware</span></span>

<span data-ttu-id="efee8-179">`HttpClient` 具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="efee8-180">`IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="efee8-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="efee8-181">简化定义应用于各命名客户端的处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="efee8-182">支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="efee8-183">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="efee8-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="efee8-184">此模式：</span><span class="sxs-lookup"><span data-stu-id="efee8-184">This pattern:</span></span>

  * <span data-ttu-id="efee8-185">类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="efee8-186">提供一种机制来管理有关 HTTP 请求的横切关注点，例如：</span><span class="sxs-lookup"><span data-stu-id="efee8-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="efee8-187">缓存</span><span class="sxs-lookup"><span data-stu-id="efee8-187">caching</span></span>
    * <span data-ttu-id="efee8-188">错误处理</span><span class="sxs-lookup"><span data-stu-id="efee8-188">error handling</span></span>
    * <span data-ttu-id="efee8-189">序列化</span><span class="sxs-lookup"><span data-stu-id="efee8-189">serialization</span></span>
    * <span data-ttu-id="efee8-190">logging</span><span class="sxs-lookup"><span data-stu-id="efee8-190">logging</span></span>

<span data-ttu-id="efee8-191">创建委托处理程序：</span><span class="sxs-lookup"><span data-stu-id="efee8-191">To create a delegating handler:</span></span>

* <span data-ttu-id="efee8-192">派生自 <xref:System.Net.Http.DelegatingHandler>。</span><span class="sxs-lookup"><span data-stu-id="efee8-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="efee8-193">重写 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="efee8-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="efee8-194">在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="efee8-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="efee8-195">上述代码检查请求中是否存在 `X-API-KEY` 标头。</span><span class="sxs-lookup"><span data-stu-id="efee8-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="efee8-196">如果缺失 `X-API-KEY`，则返回 <xref:System.Net.HttpStatusCode.BadRequest>。</span><span class="sxs-lookup"><span data-stu-id="efee8-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="efee8-197">可以使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> 将多个处理程序添加到 `HttpClient` 的配置中：</span><span class="sxs-lookup"><span data-stu-id="efee8-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="efee8-198">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="efee8-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="efee8-199">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="efee8-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="efee8-200">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="efee8-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="efee8-201">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="efee8-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="efee8-202">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="efee8-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="efee8-203">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="efee8-204">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="efee8-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="efee8-205">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="efee8-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="efee8-206">使用 [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) 将数据传递到处理程序中。</span><span class="sxs-lookup"><span data-stu-id="efee8-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="efee8-207">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="efee8-208">创建自定义 <xref:System.Threading.AsyncLocal`1> 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="efee8-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="efee8-209">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-209">Use Polly-based handlers</span></span>

<span data-ttu-id="efee8-210">`IHttpClientFactory` 与第三方库 [Polly](https://github.com/App-vNext/Polly) 集成。</span><span class="sxs-lookup"><span data-stu-id="efee8-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="efee8-211">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="efee8-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="efee8-212">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="efee8-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="efee8-213">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="efee8-214">Polly 扩展支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="efee8-215">Polly 需要 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="efee8-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="efee8-216">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="efee8-216">Handle transient faults</span></span>

<span data-ttu-id="efee8-217">错误通常在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="efee8-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允许定义一个策略来处理暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="efee8-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="efee8-219">使用 `AddTransientHttpErrorPolicy` 配置的策略处理以下响应：</span><span class="sxs-lookup"><span data-stu-id="efee8-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="efee8-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="efee8-220">HTTP 5xx</span></span>
* <span data-ttu-id="efee8-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="efee8-221">HTTP 408</span></span>

<span data-ttu-id="efee8-222">`AddTransientHttpErrorPolicy` 提供对 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="efee8-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="efee8-223">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="efee8-224">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="efee8-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="efee8-225">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="efee8-225">Dynamically select policies</span></span>

<span data-ttu-id="efee8-226">提供了扩展方法来添加基于 Polly 的处理程序，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>。</span><span class="sxs-lookup"><span data-stu-id="efee8-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="efee8-227">以下 `AddPolicyHandler` 重载检查请求以确定要应用的策略：</span><span class="sxs-lookup"><span data-stu-id="efee8-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="efee8-228">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="efee8-229">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="efee8-230">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="efee8-231">这对嵌套 Polly 策略很常见：</span><span class="sxs-lookup"><span data-stu-id="efee8-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="efee8-232">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="efee8-232">In the preceding example:</span></span>

* <span data-ttu-id="efee8-233">添加了两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-233">Two handlers are added.</span></span>
* <span data-ttu-id="efee8-234">第一个处理程序使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="efee8-235">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="efee8-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="efee8-236">第二个 `AddTransientHttpErrorPolicy` 调用添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="efee8-237">如果尝试连续失败了 5 次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="efee8-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="efee8-238">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="efee8-239">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="efee8-240">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="efee8-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="efee8-241">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="efee8-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="efee8-242">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="efee8-242">In the following code:</span></span>

* <span data-ttu-id="efee8-243">添加了“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="efee8-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 从注册表中添加“常规”和“长”策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="efee8-245">有关 `IHttpClientFactory` 和 Polly 集成的详细信息，请参阅 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="efee8-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="efee8-246">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="efee8-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="efee8-247">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-248">每个命名客户端都创建一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="efee8-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="efee8-249">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="efee8-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="efee8-250">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="efee8-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="efee8-251">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="efee8-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="efee8-252">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="efee8-253">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="efee8-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="efee8-254">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS（域名系统）更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="efee8-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="efee8-255">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="efee8-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="efee8-256">可在每个命名客户端上重写默认值：</span><span class="sxs-lookup"><span data-stu-id="efee8-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="efee8-257">`HttpClient` 实例通常可视为无需处置的 .NET 对象  。</span><span class="sxs-lookup"><span data-stu-id="efee8-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="efee8-258">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="efee8-259">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="efee8-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="efee8-260">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-261">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="efee8-262">Logging</span><span class="sxs-lookup"><span data-stu-id="efee8-262">Logging</span></span>

<span data-ttu-id="efee8-263">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-263">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="efee8-264">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-264">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="efee8-265">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="efee8-265">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="efee8-266">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="efee8-266">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="efee8-267">例如，名为 MyNamedClient 的客户端记录类别为“System.Net.Http.HttpClient.MyNamedClient.LogicalHandler”的消息   。</span><span class="sxs-lookup"><span data-stu-id="efee8-267">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="efee8-268">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-268">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="efee8-269">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-269">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="efee8-270">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-270">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="efee8-271">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-271">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="efee8-272">在 MyNamedClient 示例中，这些消息的日志类别为“System.Net.Http.HttpClient.MyNamedClient.ClientHandler”   。</span><span class="sxs-lookup"><span data-stu-id="efee8-272">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="efee8-273">在请求时，在所有其他处理程序运行后，以及刚好要发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-273">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="efee8-274">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-274">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="efee8-275">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-275">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="efee8-276">这可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-276">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="efee8-277">通过在日志类别中包含客户端名称，可以对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="efee8-277">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="efee8-278">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="efee8-278">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="efee8-279">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="efee8-279">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="efee8-280">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="efee8-280">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="efee8-281"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="efee8-281">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="efee8-282">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="efee8-282">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="efee8-283">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="efee8-283">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="efee8-284">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="efee8-284">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="efee8-285">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="efee8-285">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="efee8-286">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="efee8-286">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="efee8-287">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="efee8-287">In the following example:</span></span>

* <span data-ttu-id="efee8-288"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="efee8-288"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="efee8-289">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-289">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="efee8-290">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="efee8-290">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="efee8-291">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="efee8-291">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="efee8-292">其他资源</span><span class="sxs-lookup"><span data-stu-id="efee8-292">Additional resources</span></span>

* [<span data-ttu-id="efee8-293">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="efee8-293">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="efee8-294">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="efee8-294">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="efee8-295">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="efee8-295">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="efee8-296">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="efee8-296">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="efee8-297">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-297">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="efee8-298">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="efee8-298">It offers the following benefits:</span></span>

* <span data-ttu-id="efee8-299">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-299">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="efee8-300">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="efee8-300">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="efee8-301">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="efee8-301">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="efee8-302">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="efee8-302">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="efee8-303">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="efee8-303">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="efee8-304">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-304">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="efee8-305">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="efee8-305">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="efee8-306">消耗模式</span><span class="sxs-lookup"><span data-stu-id="efee8-306">Consumption patterns</span></span>

<span data-ttu-id="efee8-307">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="efee8-307">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="efee8-308">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-308">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="efee8-309">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-309">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="efee8-310">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-310">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="efee8-311">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-311">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="efee8-312">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="efee8-312">None of them are strictly superior to another.</span></span> <span data-ttu-id="efee8-313">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="efee8-313">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="efee8-314">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-314">Basic usage</span></span>

<span data-ttu-id="efee8-315">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="efee8-315">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="efee8-316">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="efee8-316">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="efee8-317">`IHttpClientFactory` 可以用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="efee8-317">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="efee8-318">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="efee8-318">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="efee8-319">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="efee8-319">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="efee8-320">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="efee8-320">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="efee8-321">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-321">Named clients</span></span>

<span data-ttu-id="efee8-322">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="efee8-322">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="efee8-323">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="efee8-323">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="efee8-324">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="efee8-324">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="efee8-325">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="efee8-325">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="efee8-326">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="efee8-326">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="efee8-327">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-327">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="efee8-328">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="efee8-328">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="efee8-329">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="efee8-329">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="efee8-330">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="efee8-330">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="efee8-331">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-331">Typed clients</span></span>

<span data-ttu-id="efee8-332">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-332">Typed clients:</span></span>

* <span data-ttu-id="efee8-333">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="efee8-333">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="efee8-334">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="efee8-334">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="efee8-335">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="efee8-335">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="efee8-336">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="efee8-336">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="efee8-337">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="efee8-337">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="efee8-338">类型化客户端在构造函数中接收 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="efee8-338">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="efee8-339">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="efee8-339">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="efee8-340">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="efee8-340">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="efee8-341">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="efee8-341">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="efee8-342">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="efee8-342">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="efee8-343">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="efee8-343">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="efee8-344">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-344">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="efee8-345">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-345">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="efee8-346">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="efee8-346">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="efee8-347">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="efee8-347">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="efee8-348">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-348">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="efee8-349">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="efee8-349">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="efee8-350">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="efee8-350">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="efee8-351">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-351">Generated clients</span></span>

<span data-ttu-id="efee8-352">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="efee8-352">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="efee8-353">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="efee8-353">Refit is a REST library for .NET.</span></span> <span data-ttu-id="efee8-354">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="efee8-354">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="efee8-355">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="efee8-355">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="efee8-356">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="efee8-356">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="efee8-357">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-357">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="efee8-358">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-358">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="efee8-359">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="efee8-359">Outgoing request middleware</span></span>

<span data-ttu-id="efee8-360">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-360">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="efee8-361">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-361">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="efee8-362">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-362">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="efee8-363">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="efee8-363">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="efee8-364">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-364">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="efee8-365">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="efee8-365">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="efee8-366">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="efee8-366">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="efee8-367">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="efee8-367">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="efee8-368">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-368">The preceding code defines a basic handler.</span></span> <span data-ttu-id="efee8-369">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="efee8-369">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="efee8-370">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="efee8-370">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="efee8-371">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置。</span><span class="sxs-lookup"><span data-stu-id="efee8-371">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="efee8-372">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="efee8-372">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="efee8-373">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="efee8-373">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="efee8-374">`IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。</span><span class="sxs-lookup"><span data-stu-id="efee8-374">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="efee8-375">处理程序可依赖于任何作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="efee8-375">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="efee8-376">处理程序依赖的服务会在处置处理程序时得到处置。</span><span class="sxs-lookup"><span data-stu-id="efee8-376">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="efee8-377">注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。</span><span class="sxs-lookup"><span data-stu-id="efee8-377">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="efee8-378">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-378">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="efee8-379">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="efee8-379">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="efee8-380">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="efee8-380">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="efee8-381">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-381">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="efee8-382">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-382">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="efee8-383">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="efee8-383">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="efee8-384">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-384">Use Polly-based handlers</span></span>

<span data-ttu-id="efee8-385">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="efee8-385">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="efee8-386">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="efee8-386">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="efee8-387">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="efee8-387">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="efee8-388">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-388">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="efee8-389">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="efee8-389">The Polly extensions:</span></span>

* <span data-ttu-id="efee8-390">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-390">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="efee8-391">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="efee8-391">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="efee8-392">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="efee8-392">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="efee8-393">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="efee8-393">Handle transient faults</span></span>

<span data-ttu-id="efee8-394">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-394">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="efee8-395">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="efee8-395">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="efee8-396">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="efee8-396">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="efee8-397">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="efee8-397">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="efee8-398">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="efee8-398">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="efee8-399">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-399">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="efee8-400">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="efee8-400">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="efee8-401">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="efee8-401">Dynamically select policies</span></span>

<span data-ttu-id="efee8-402">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-402">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="efee8-403">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="efee8-403">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="efee8-404">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="efee8-404">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="efee8-405">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-405">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="efee8-406">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-406">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="efee8-407">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-407">Add multiple Polly handlers</span></span>

<span data-ttu-id="efee8-408">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="efee8-408">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="efee8-409">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-409">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="efee8-410">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-410">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="efee8-411">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="efee8-411">Failed requests are retried up to three times.</span></span> <span data-ttu-id="efee8-412">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-412">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="efee8-413">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="efee8-413">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="efee8-414">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-414">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="efee8-415">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-415">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="efee8-416">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="efee8-416">Add policies from the Polly registry</span></span>

<span data-ttu-id="efee8-417">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="efee8-417">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="efee8-418">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="efee8-418">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="efee8-419">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="efee8-419">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="efee8-420">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="efee8-420">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="efee8-421">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="efee8-421">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="efee8-422">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="efee8-422">HttpClient and lifetime management</span></span>

<span data-ttu-id="efee8-423">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-423">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-424">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="efee8-424">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="efee8-425">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="efee8-425">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="efee8-426">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="efee8-426">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="efee8-427">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="efee8-427">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="efee8-428">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-428">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="efee8-429">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="efee8-429">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="efee8-430">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="efee8-430">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="efee8-431">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="efee8-431">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="efee8-432">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="efee8-432">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="efee8-433">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="efee8-433">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="efee8-434">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-434">Disposal of the client isn't required.</span></span> <span data-ttu-id="efee8-435">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-435">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="efee8-436">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="efee8-436">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="efee8-437">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="efee8-437">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="efee8-438">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-438">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-439">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-439">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="efee8-440">Logging</span><span class="sxs-lookup"><span data-stu-id="efee8-440">Logging</span></span>

<span data-ttu-id="efee8-441">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-441">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="efee8-442">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-442">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="efee8-443">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="efee8-443">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="efee8-444">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="efee8-444">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="efee8-445">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-445">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="efee8-446">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-446">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="efee8-447">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-447">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="efee8-448">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-448">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="efee8-449">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-449">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="efee8-450">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="efee8-450">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="efee8-451">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-451">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="efee8-452">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-452">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="efee8-453">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-453">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="efee8-454">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-454">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="efee8-455">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="efee8-455">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="efee8-456">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="efee8-456">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="efee8-457">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="efee8-457">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="efee8-458">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="efee8-458">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="efee8-459"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="efee8-459">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="efee8-460">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="efee8-460">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="efee8-461">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="efee8-461">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="efee8-462">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="efee8-462">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="efee8-463">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="efee8-463">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="efee8-464">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="efee8-464">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="efee8-465">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="efee8-465">In the following example:</span></span>

* <span data-ttu-id="efee8-466"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="efee8-466"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="efee8-467">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-467">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="efee8-468">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="efee8-468">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="efee8-469">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="efee8-469">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="efee8-470">其他资源</span><span class="sxs-lookup"><span data-stu-id="efee8-470">Additional resources</span></span>

* [<span data-ttu-id="efee8-471">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="efee8-471">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="efee8-472">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="efee8-472">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="efee8-473">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="efee8-473">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="efee8-474">作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="efee8-474">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="efee8-475">可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-475">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="efee8-476">这能带来以下好处：</span><span class="sxs-lookup"><span data-stu-id="efee8-476">It offers the following benefits:</span></span>

* <span data-ttu-id="efee8-477">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-477">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="efee8-478">例如，可注册和配置 github 客户端，使其访问 [GitHub](https://github.com/)  。</span><span class="sxs-lookup"><span data-stu-id="efee8-478">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="efee8-479">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="efee8-479">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="efee8-480">通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。</span><span class="sxs-lookup"><span data-stu-id="efee8-480">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="efee8-481">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="efee8-481">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="efee8-482">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-482">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="efee8-483">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="efee8-483">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="efee8-484">系统必备</span><span class="sxs-lookup"><span data-stu-id="efee8-484">Prerequisites</span></span>

<span data-ttu-id="efee8-485">面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="efee8-485">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="efee8-486">面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。</span><span class="sxs-lookup"><span data-stu-id="efee8-486">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="efee8-487">消耗模式</span><span class="sxs-lookup"><span data-stu-id="efee8-487">Consumption patterns</span></span>

<span data-ttu-id="efee8-488">在应用中可以通过以下多种方式使用 `IHttpClientFactory`：</span><span class="sxs-lookup"><span data-stu-id="efee8-488">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="efee8-489">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-489">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="efee8-490">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-490">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="efee8-491">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-491">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="efee8-492">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-492">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="efee8-493">它们之间不存在严格的优先级。</span><span class="sxs-lookup"><span data-stu-id="efee8-493">None of them are strictly superior to another.</span></span> <span data-ttu-id="efee8-494">最佳方法取决于应用的约束条件。</span><span class="sxs-lookup"><span data-stu-id="efee8-494">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="efee8-495">基本用法</span><span class="sxs-lookup"><span data-stu-id="efee8-495">Basic usage</span></span>

<span data-ttu-id="efee8-496">在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="efee8-496">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="efee8-497">注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。</span><span class="sxs-lookup"><span data-stu-id="efee8-497">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="efee8-498">`IHttpClientFactory` 可以用于创建 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="efee8-498">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="efee8-499">以这种方式使用 `IHttpClientFactory` 适合重构现有应用。</span><span class="sxs-lookup"><span data-stu-id="efee8-499">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="efee8-500">这不会影响 `HttpClient` 的使用方式。</span><span class="sxs-lookup"><span data-stu-id="efee8-500">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="efee8-501">在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。</span><span class="sxs-lookup"><span data-stu-id="efee8-501">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="efee8-502">命名客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-502">Named clients</span></span>

<span data-ttu-id="efee8-503">如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端  。</span><span class="sxs-lookup"><span data-stu-id="efee8-503">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="efee8-504">可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。</span><span class="sxs-lookup"><span data-stu-id="efee8-504">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="efee8-505">上面的代码调用 `AddHttpClient`，同时提供名称“github”  。</span><span class="sxs-lookup"><span data-stu-id="efee8-505">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="efee8-506">此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。</span><span class="sxs-lookup"><span data-stu-id="efee8-506">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="efee8-507">每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。</span><span class="sxs-lookup"><span data-stu-id="efee8-507">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="efee8-508">要使用命名客户端，可将字符串参数传递到 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-508">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="efee8-509">指定要创建的客户端的名称：</span><span class="sxs-lookup"><span data-stu-id="efee8-509">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="efee8-510">在上述代码中，请求不需要指定主机名。</span><span class="sxs-lookup"><span data-stu-id="efee8-510">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="efee8-511">可以仅传递路径，因为采用了为客户端配置的基址。</span><span class="sxs-lookup"><span data-stu-id="efee8-511">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="efee8-512">类型化客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-512">Typed clients</span></span>

<span data-ttu-id="efee8-513">类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-513">Typed clients:</span></span>

* <span data-ttu-id="efee8-514">提供与命名客户端一样的功能，不需要将字符串用作密钥。</span><span class="sxs-lookup"><span data-stu-id="efee8-514">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="efee8-515">在使用客户端时提供 IntelliSense 和编译器帮助。</span><span class="sxs-lookup"><span data-stu-id="efee8-515">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="efee8-516">提供单个位置来配置特定 `HttpClient` 并与其进行交互。</span><span class="sxs-lookup"><span data-stu-id="efee8-516">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="efee8-517">例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="efee8-517">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="efee8-518">使用 DI 且可以被注入到应用中需要的位置。</span><span class="sxs-lookup"><span data-stu-id="efee8-518">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="efee8-519">类型化客户端在构造函数中接收 `HttpClient` 参数：</span><span class="sxs-lookup"><span data-stu-id="efee8-519">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="efee8-520">在上述代码中，配置转移到了类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="efee8-520">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="efee8-521">`HttpClient` 对象公开为公共属性。</span><span class="sxs-lookup"><span data-stu-id="efee8-521">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="efee8-522">可以定义公开 `HttpClient` 功能的特定于 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="efee8-522">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="efee8-523">`GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。</span><span class="sxs-lookup"><span data-stu-id="efee8-523">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="efee8-524">要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：</span><span class="sxs-lookup"><span data-stu-id="efee8-524">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="efee8-525">使用 DI 将类型客户端注册为暂时客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-525">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="efee8-526">可以直接插入或使用类型化客户端：</span><span class="sxs-lookup"><span data-stu-id="efee8-526">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="efee8-527">根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：</span><span class="sxs-lookup"><span data-stu-id="efee8-527">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="efee8-528">可以将 `HttpClient` 完全封装在类型化客户端中。</span><span class="sxs-lookup"><span data-stu-id="efee8-528">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="efee8-529">不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-529">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="efee8-530">在上述代码中，`HttpClient` 存储未私有字段。</span><span class="sxs-lookup"><span data-stu-id="efee8-530">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="efee8-531">进行外部调用的所有访问都经由 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="efee8-531">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="efee8-532">生成的客户端</span><span class="sxs-lookup"><span data-stu-id="efee8-532">Generated clients</span></span>

<span data-ttu-id="efee8-533">`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。</span><span class="sxs-lookup"><span data-stu-id="efee8-533">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="efee8-534">Refit 是.NET 的 REST 库。</span><span class="sxs-lookup"><span data-stu-id="efee8-534">Refit is a REST library for .NET.</span></span> <span data-ttu-id="efee8-535">它将 REST API 转换为实时接口。</span><span class="sxs-lookup"><span data-stu-id="efee8-535">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="efee8-536">`RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。</span><span class="sxs-lookup"><span data-stu-id="efee8-536">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="efee8-537">定义了接口和答复来代表外部 API 及其响应：</span><span class="sxs-lookup"><span data-stu-id="efee8-537">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="efee8-538">可以添加类型化客户端，使用 Refit 生成实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-538">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="efee8-539">可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：</span><span class="sxs-lookup"><span data-stu-id="efee8-539">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="efee8-540">出站请求中间件</span><span class="sxs-lookup"><span data-stu-id="efee8-540">Outgoing request middleware</span></span>

<span data-ttu-id="efee8-541">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-541">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="efee8-542">`IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-542">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="efee8-543">它支持注册和链接多个处理程序，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-543">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="efee8-544">每个处理程序都可以在出站请求前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="efee8-544">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="efee8-545">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="efee8-545">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="efee8-546">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="efee8-546">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="efee8-547">要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。</span><span class="sxs-lookup"><span data-stu-id="efee8-547">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="efee8-548">重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="efee8-548">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="efee8-549">上述代码定义了基本处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-549">The preceding code defines a basic handler.</span></span> <span data-ttu-id="efee8-550">它检查请求中是否包含 `X-API-KEY` 头。</span><span class="sxs-lookup"><span data-stu-id="efee8-550">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="efee8-551">如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。</span><span class="sxs-lookup"><span data-stu-id="efee8-551">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="efee8-552">在注册期间可将一个或多个标头添加到 `HttpClient` 的配置。</span><span class="sxs-lookup"><span data-stu-id="efee8-552">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="efee8-553">此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。</span><span class="sxs-lookup"><span data-stu-id="efee8-553">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="efee8-554">在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。</span><span class="sxs-lookup"><span data-stu-id="efee8-554">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="efee8-555">处理程序必须  在 DI 中注册为暂时性服务且从不设置作用域。</span><span class="sxs-lookup"><span data-stu-id="efee8-555">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="efee8-556">如果该处理程序注册为作用域服务，并且处理程序依赖的任何服务都是可释放的：</span><span class="sxs-lookup"><span data-stu-id="efee8-556">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="efee8-557">处理程序的服务可以在处理程序超出作用域之前被释放。</span><span class="sxs-lookup"><span data-stu-id="efee8-557">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="efee8-558">已释放的处理程序服务可导致处理程序失败。</span><span class="sxs-lookup"><span data-stu-id="efee8-558">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="efee8-559">注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。</span><span class="sxs-lookup"><span data-stu-id="efee8-559">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="efee8-560">可以按处理程序应该执行的顺序注册多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-560">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="efee8-561">每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：</span><span class="sxs-lookup"><span data-stu-id="efee8-561">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="efee8-562">使用以下方法之一将每个请求状态与消息处理程序共享：</span><span class="sxs-lookup"><span data-stu-id="efee8-562">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="efee8-563">使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-563">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="efee8-564">使用 `IHttpContextAccessor` 访问当前请求。</span><span class="sxs-lookup"><span data-stu-id="efee8-564">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="efee8-565">创建自定义 `AsyncLocal` 存储对象以传递数据。</span><span class="sxs-lookup"><span data-stu-id="efee8-565">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="efee8-566">使用基于 Polly 的处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-566">Use Polly-based handlers</span></span>

<span data-ttu-id="efee8-567">`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。</span><span class="sxs-lookup"><span data-stu-id="efee8-567">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="efee8-568">Polly 是适用于 .NET 的全面恢复和临时故障处理库。</span><span class="sxs-lookup"><span data-stu-id="efee8-568">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="efee8-569">开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。</span><span class="sxs-lookup"><span data-stu-id="efee8-569">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="efee8-570">提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-570">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="efee8-571">Polly 扩展：</span><span class="sxs-lookup"><span data-stu-id="efee8-571">The Polly extensions:</span></span>

* <span data-ttu-id="efee8-572">支持将基于 Polly 的处理程序添加到客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-572">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="efee8-573">安装了 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包后可使用该扩展。</span><span class="sxs-lookup"><span data-stu-id="efee8-573">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="efee8-574">ASP.NET Core 共享框架中不包括该包。</span><span class="sxs-lookup"><span data-stu-id="efee8-574">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="efee8-575">处理临时故障</span><span class="sxs-lookup"><span data-stu-id="efee8-575">Handle transient faults</span></span>

<span data-ttu-id="efee8-576">大多数常见错误在暂时执行外部 HTTP 调用时发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-576">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="efee8-577">包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。</span><span class="sxs-lookup"><span data-stu-id="efee8-577">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="efee8-578">使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。</span><span class="sxs-lookup"><span data-stu-id="efee8-578">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="efee8-579">`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。</span><span class="sxs-lookup"><span data-stu-id="efee8-579">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="efee8-580">该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：</span><span class="sxs-lookup"><span data-stu-id="efee8-580">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="efee8-581">上述代码中定义了 `WaitAndRetryAsync` 策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-581">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="efee8-582">请求失败后最多可以重试三次，每次尝试间隔 600 ms。</span><span class="sxs-lookup"><span data-stu-id="efee8-582">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="efee8-583">动态选择策略</span><span class="sxs-lookup"><span data-stu-id="efee8-583">Dynamically select policies</span></span>

<span data-ttu-id="efee8-584">存在其他扩展方法，可以用于添加基于 Polly 的处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-584">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="efee8-585">这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。</span><span class="sxs-lookup"><span data-stu-id="efee8-585">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="efee8-586">一个重载允许在定义要应用的策略时检查该请求：</span><span class="sxs-lookup"><span data-stu-id="efee8-586">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="efee8-587">在上述代码中，如果出站请求为 HTTP GET，则应用 10 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-587">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="efee8-588">其他所有 HTTP 方法应用 30 秒超时。</span><span class="sxs-lookup"><span data-stu-id="efee8-588">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="efee8-589">添加多个 Polly 处理程序</span><span class="sxs-lookup"><span data-stu-id="efee8-589">Add multiple Polly handlers</span></span>

<span data-ttu-id="efee8-590">通过嵌套 Polly 策略来增强功能是很常见的：</span><span class="sxs-lookup"><span data-stu-id="efee8-590">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="efee8-591">在上述示例中，添加两个处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-591">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="efee8-592">第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-592">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="efee8-593">若请求失败，最多可重试三次。</span><span class="sxs-lookup"><span data-stu-id="efee8-593">Failed requests are retried up to three times.</span></span> <span data-ttu-id="efee8-594">第二个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。</span><span class="sxs-lookup"><span data-stu-id="efee8-594">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="efee8-595">如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。</span><span class="sxs-lookup"><span data-stu-id="efee8-595">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="efee8-596">断路器策略处于监控状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-596">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="efee8-597">通过此客户端进行的所有调用都共享同样的线路状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-597">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="efee8-598">从 Polly 注册表添加策略</span><span class="sxs-lookup"><span data-stu-id="efee8-598">Add policies from the Polly registry</span></span>

<span data-ttu-id="efee8-599">管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。</span><span class="sxs-lookup"><span data-stu-id="efee8-599">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="efee8-600">提供了一种扩展方法，可以使用注册表中的策略添加处理程序：</span><span class="sxs-lookup"><span data-stu-id="efee8-600">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="efee8-601">在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。</span><span class="sxs-lookup"><span data-stu-id="efee8-601">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="efee8-602">若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。</span><span class="sxs-lookup"><span data-stu-id="efee8-602">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="efee8-603">要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="efee8-603">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="efee8-604">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="efee8-604">HttpClient and lifetime management</span></span>

<span data-ttu-id="efee8-605">每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-605">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-606">每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="efee8-606">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="efee8-607">工厂管理 `HttpMessageHandler` 实例的生存期。</span><span class="sxs-lookup"><span data-stu-id="efee8-607">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="efee8-608">`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。</span><span class="sxs-lookup"><span data-stu-id="efee8-608">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="efee8-609">新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。</span><span class="sxs-lookup"><span data-stu-id="efee8-609">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="efee8-610">由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。</span><span class="sxs-lookup"><span data-stu-id="efee8-610">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="efee8-611">创建超出必要数量的处理程序可能会导致连接延迟。</span><span class="sxs-lookup"><span data-stu-id="efee8-611">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="efee8-612">部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。</span><span class="sxs-lookup"><span data-stu-id="efee8-612">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="efee8-613">处理程序的默认生存期为两分钟。</span><span class="sxs-lookup"><span data-stu-id="efee8-613">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="efee8-614">可在每个命名客户端上重写默认值。</span><span class="sxs-lookup"><span data-stu-id="efee8-614">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="efee8-615">要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="efee8-615">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="efee8-616">无需处置客户端。</span><span class="sxs-lookup"><span data-stu-id="efee8-616">Disposal of the client isn't required.</span></span> <span data-ttu-id="efee8-617">处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="efee8-617">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="efee8-618">`IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。</span><span class="sxs-lookup"><span data-stu-id="efee8-618">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="efee8-619">`HttpClient` 实例通常可视为无需处置的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="efee8-619">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="efee8-620">保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-620">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="efee8-621">迁移到 `IHttpClientFactory` 后，就无需再使用此模式。</span><span class="sxs-lookup"><span data-stu-id="efee8-621">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="efee8-622">Logging</span><span class="sxs-lookup"><span data-stu-id="efee8-622">Logging</span></span>

<span data-ttu-id="efee8-623">通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-623">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="efee8-624">在日志记录配置中启用合适的信息级别可以查看默认日志消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-624">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="efee8-625">仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。</span><span class="sxs-lookup"><span data-stu-id="efee8-625">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="efee8-626">用于每个客户端的日志类别包含客户端名称。</span><span class="sxs-lookup"><span data-stu-id="efee8-626">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="efee8-627">例如，名为“MyNamedClient”  的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-627">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="efee8-628">后缀为 LogicalHandler  的消息在请求处理程序管道外部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-628">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="efee8-629">在请求时，在管道中的任何其他处理程序处理请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-629">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="efee8-630">在响应时，在任何其他管道处理程序接收响应之后记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-630">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="efee8-631">日志记录还在请求处理程序管道内部发生。</span><span class="sxs-lookup"><span data-stu-id="efee8-631">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="efee8-632">在“MyNamedClient”  示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。</span><span class="sxs-lookup"><span data-stu-id="efee8-632">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="efee8-633">在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。</span><span class="sxs-lookup"><span data-stu-id="efee8-633">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="efee8-634">在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。</span><span class="sxs-lookup"><span data-stu-id="efee8-634">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="efee8-635">在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-635">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="efee8-636">例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。</span><span class="sxs-lookup"><span data-stu-id="efee8-636">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="efee8-637">通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。</span><span class="sxs-lookup"><span data-stu-id="efee8-637">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="efee8-638">配置 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="efee8-638">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="efee8-639">控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。</span><span class="sxs-lookup"><span data-stu-id="efee8-639">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="efee8-640">在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="efee8-640">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="efee8-641"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。</span><span class="sxs-lookup"><span data-stu-id="efee8-641">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="efee8-642">委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="efee8-642">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="efee8-643">在控制台应用中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="efee8-643">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="efee8-644">在控制台中，将以下包引用添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="efee8-644">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="efee8-645">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="efee8-645">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="efee8-646">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="efee8-646">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="efee8-647">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="efee8-647">In the following example:</span></span>

* <span data-ttu-id="efee8-648"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主机的](xref:fundamentals/host/generic-host)服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="efee8-648"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="efee8-649">`MyService` 从服务创建客户端工厂实例，用于创建 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="efee8-649">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="efee8-650">`HttpClient` 用于检索网页。</span><span class="sxs-lookup"><span data-stu-id="efee8-650">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="efee8-651">`Main` 可创建作用域来执行服务的 `GetPage` 方法，并将网页内容的前 500 个字符写入控制台。</span><span class="sxs-lookup"><span data-stu-id="efee8-651">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="efee8-652">其他资源</span><span class="sxs-lookup"><span data-stu-id="efee8-652">Additional resources</span></span>

* [<span data-ttu-id="efee8-653">使用 HttpClientFactory 来实现复原 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="efee8-653">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="efee8-654">通过 HttpClientFactory 和 Polly 策略实现使用指数退避算法的 HTTP 调用重试</span><span class="sxs-lookup"><span data-stu-id="efee8-654">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="efee8-655">实现断路器模式</span><span class="sxs-lookup"><span data-stu-id="efee8-655">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
