---
title: 启动 HTTP 请求
author: stevejgordon
description: 了解如何将 IHttpClientFactory 接口用于管理 ASP.NET Core 中的逻辑 HttpClient 实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/25/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 4fc4e602b809563ea78b6a3af5e5eb5c0ebeddea
ms.sourcegitcommit: c6db8b14521814f1f7e528d7aa06e474e4c04a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "55065030"
---
# <a name="initiate-http-requests"></a>启动 HTTP 请求

作者：[Glenn Condron](https://github.com/glennc)[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)

可以注册 <xref:System.Net.Http.IHttpClientFactory> 并将其用于配置和创建应用中的 <xref:System.Net.Http.HttpClient> 实例。 这能带来以下好处：

* 提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。 例如，可以注册 github 客户端，并将它配置为访问 GitHub。 可以注册一个默认客户端用于其他用途。
* 通过委托 `HttpClient` 中的处理程序整理出站中间件的概念，并提供适用于基于 Polly 的中间件的扩展来利用概念。
* 管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。
* （通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

面向.NET Framework 的项目要求安装 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。 面向 .NET Core 且引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的项目已经包括 `Microsoft.Extensions.Http` 包。

## <a name="consumption-patterns"></a>消耗模式

在应用中可以通过以下多种方式使用 `IHttpClientFactory`：

* [基本用法](#basic-usage)
* [命名客户端](#named-clients)
* [类型化客户端](#typed-clients)
* [生成的客户端](#generated-clients)

它们之间不存在严格的优先级。 最佳方法取决于应用的约束条件。

### <a name="basic-usage"></a>基本用法

在 `Startup.ConfigureServices` 方法中，通过在 `IServiceCollection` 上调用 `AddHttpClient` 扩展方法可以注册 `IHttpClientFactory`。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

注册后，在可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 注入服务的任何位置，代码都能接受 `IHttpClientFactory`。 `IHttpClientFactory` 可以用于创建 `HttpClient` 实例：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

以这种方式使用 `IHttpClientFactory` 适合重构现有应用。 这不会影响 `HttpClient` 的使用方式。 在当前创建 `HttpClient` 实例的位置，使用对 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 的调用替换这些匹配项。

### <a name="named-clients"></a>命名客户端

如果应用需要有许多不同的 `HttpClient` 用法（每种用法的配置都不同），可以视情况使用命名客户端。 可以在 `HttpClient` 中注册时指定命名 `Startup.ConfigureServices` 的配置。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

上面的代码调用 `AddHttpClient`，同时提供名称“github”。 此客户端应用了一些默认配置，也就是需要基址和两个标头来使用 GitHub API。

每次调用 `CreateClient` 时，都会创建 `HttpClient` 的新实例，并调用配置操作。

要使用命名客户端，可将字符串参数传递到 `CreateClient`。 指定要创建的客户端的名称：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

在上述代码中，请求不需要指定主机名。 可以仅传递路径，因为采用了为客户端配置的基址。

### <a name="typed-clients"></a>类型化客户端

类型化客户端提供与命名客户端一样的功能，不需要将字符串用作密钥。 类型化客户端方法在使用客户端时提供 IntelliSense 和编译器帮助。 它们提供单个地址来配置特定 `HttpClient` 并与其进行交互。 例如，单个类型化客户端可能用于单个后端终结点，并封装此终结点的所有处理逻辑。 另一个优势是它们使用 DI 且可以被注入到应用中需要的位置。

类型化客户端在构造函数中接收 `HttpClient` 参数：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

在上述代码中，配置转移到了类型化客户端中。 `HttpClient` 对象公开为公共属性。 可以定义公开 `HttpClient` 功能的特定于 API 的方法。 `GetAspNetDocsIssues` 方法从 GitHub 存储库封装查询和分析最新待解决问题所需的代码。

要注册类型化客户端，可在 `Startup.ConfigureServices` 中使用通用的 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 扩展方法，指定类型化客户端类：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

使用 DI 将类型客户端注册为暂时客户端。 可以直接插入或使用类型化客户端：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

根据你的喜好，可以在 `Startup.ConfigureServices` 中注册时指定类型化客户端的配置，而不是在类型化客户端的构造函数中指定：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

可以将 `HttpClient` 完全封装在类型化客户端中。 不是将它公开为属性，而是可以提供公共方法，用于在内部调用 `HttpClient`。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

在上述代码中，`HttpClient` 存储未私有字段。 进行外部调用的所有访问都经由 `GetRepos` 方法。

### <a name="generated-clients"></a>生成的客户端

`IHttpClientFactory` 可结合其他第三方库（例如 [Refit](https://github.com/paulcbetts/refit)）使用。 Refit 是.NET 的 REST 库。 它将 REST API 转换为实时接口。 `RestService` 动态生成该接口的实现，使用 `HttpClient` 进行外部 HTTP 调用。

定义了接口和答复来代表外部 API 及其响应：

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

可以添加类型化客户端，使用 Refit 生成实现：

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

可以在必要时使用定义的接口，以及由 DI 和 Refit 提供的实现：

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

## <a name="outgoing-request-middleware"></a>出站请求中间件

`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。 `IHttpClientFactory` 可以轻松定义处理程序并应用于每个命名客户端。 它支持注册和链接多个处理程序，以生成出站请求中间件管道。 每个处理程序都可以在出站请求前后执行工作。 此模式类似于 ASP.NET Core 中的入站中间件管道。 此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。

要创建处理程序，请定义一个派生自 <xref:System.Net.Http.DelegatingHandler> 的类。 重写 `SendAsync` 方法，在将请求传递至管道中的下一个处理程序之前执行代码：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

上述代码定义了基本处理程序。 它检查请求中是否包含 `X-API-KEY` 头。 如果标头缺失，它可以避免 HTTP 调用，并返回合适的响应。

在注册期间可将一个或多个标头添加到 `HttpClient` 的配置。 此任务通过 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的扩展方法完成。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。 `IHttpClientFactory` 为每个处理程序创建单独的 DI 作用域。 处理程序可依赖于任何作用域的服务。 处理程序依赖的服务会在处置处理程序时得到处置。

注册后可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入标头的类型。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

在上述代码中通过 DI 注册了 `ValidateHeaderHandler`。 处理程序必须在 DI 中注册为暂时性服务且从不设置作用域。 如果处理程序注册为作用域服务且处理程序依赖的任何服务都是可处置的，则处理程序的服务可在处理程序超出作用域前得到处置，而这将导致处理程序失败。

注册后，可以调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，传入处理程序类型。

::: moniker-end

可以按处理程序应该执行的顺序注册多个处理程序。 每个处理程序都会覆盖下一个处理程序，直到最终 `HttpClientHandler` 执行请求：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

使用以下方法之一将每个请求状态与消息处理程序共享：

* 使用 `HttpRequestMessage.Properties` 将数据传递到处理程序。
* 使用 `IHttpContextAccessor` 访问当前请求。
* 创建自定义 `AsyncLocal` 存储对象以传递数据。

## <a name="use-polly-based-handlers"></a>使用基于 Polly 的处理程序

`IHttpClientFactory` 与一个名为 [Polly](https://github.com/App-vNext/Polly) 的热门第三方库集成。 Polly 是适用于 .NET 的全面恢复和临时故障处理库。 开发人员通过它可以表达策略，例如以流畅且线程安全的方式处理重试、断路器、超时、Bulkhead 隔离和回退。

提供了扩展方法，以实现将 Polly 策略用于配置的 `HttpClient` 实例。 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 包中提供 Polly 扩展。 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此包。 若要使用扩展，项目中应该包括显式 `<PackageReference />`。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/HttpClientFactorySample.csproj?highlight=9)]

还原此包后，可以使用扩展方法来支持将基于 Polly 的处理程序添加至客户端。

### <a name="handle-transient-faults"></a>处理临时故障

大多数常见错误在暂时执行外部 HTTP 调用时发生。 包含了一种简便的扩展方法，该方法名为 `AddTransientHttpErrorPolicy`，允许定义策略来处理临时故障。 使用这种扩展方法配置的策略可以处理 `HttpRequestException`、HTTP 5xx 响应以及 HTTP 408 响应。

`AddTransientHttpErrorPolicy` 扩展可在 `Startup.ConfigureServices` 内使用。 该扩展可以提供 `PolicyBuilder` 对象的访问权限，该对象配置为处理表示可能的临时故障的错误：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

上述代码中定义了 `WaitAndRetryAsync` 策略。 请求失败后最多可以重试三次，每次尝试间隔 600 ms。

### <a name="dynamically-select-policies"></a>动态选择策略

存在其他扩展方法，可以用于添加基于 Polly 的处理程序。 这类扩展的其中一个是 `AddPolicyHandler`，它具备多个重载。 一个重载允许在定义要应用的策略时检查该请求：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

在上述代码中，如果出站请求为 GET，则应用 10 秒超时。 其他所有 HTTP 方法应用 30 秒超时。

### <a name="add-multiple-polly-handlers"></a>添加多个 Polly 处理程序

嵌套 Polly 策略以增强功能是很常见的：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

在上述示例中，添加两个处理程序。 第一个使用 `AddTransientHttpErrorPolicy` 扩展添加重试策略。 若请求失败，最多可重试三次。 第一个调用 `AddTransientHttpErrorPolicy` 添加断路器策略。 如果尝试连续失败了五次，则会阻止后续外部请求 30 秒。 断路器策略处于监控状态。 通过此客户端进行的所有调用都共享同样的线路状态。

### <a name="add-policies-from-the-polly-registry"></a>从 Polly 注册表添加策略

管理常用策略的一种方法是一次性定义它们并使用 `PolicyRegistry` 注册它们。 提供了一种扩展方法，可以使用注册表中的策略添加处理程序：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

在上面的代码中，两个策略在 `PolicyRegistry` 添加到 `ServiceCollection` 中时进行注册。 若要使用注册表中的策略，请使用 `AddPolicyHandlerFromRegistry` 方法，同时传递要应用的策略的名称。

要进一步了解 `IHttpClientFactory` 和 Polly 集成，请参考 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。

## <a name="httpclient-and-lifetime-management"></a>HttpClient 和生存期管理

每次对 `IHttpClientFactory` 调用 `CreateClient` 都会返回一个新 `HttpClient` 实例。 每个命名的客户端都具有一个 <xref:System.Net.Http.HttpMessageHandler>。 工厂管理 `HttpMessageHandler` 实例的生存期。

`IHttpClientFactory` 将工厂创建的 `HttpMessageHandler` 实例汇集到池中，以减少资源消耗。 新建 `HttpClient` 实例时，可能会重用池中的 `HttpMessageHandler` 实例（如果生存期尚未到期的话）。

由于每个处理程序通常管理自己的基础 HTTP 连接，因此需要池化处理程序。 创建超出必要数量的处理程序可能会导致连接延迟。 部分处理程序还保持连接无期限地打开，这样可以防止处理程序对 DNS 更改作出反应。

处理程序的默认生存期为两分钟。 可在每个命名客户端上重写默认值。 要重写该值，请在创建客户端时在返回的 `IHttpClientBuilder` 上调用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

无需处置客户端。 处置既取消传出请求，又保证在调用 <xref:System.IDisposable.Dispose*> 后无法使用给定的 `HttpClient` 实例。 `IHttpClientFactory` 跟踪和处置 `HttpClient` 实例使用的资源。 `HttpClient` 实例通常可视为无需处置的 .NET 对象。

保持各个 `HttpClient` 实例长时间处于活动状态是在 `IHttpClientFactory` 推出前使用的常见模式。 迁移到 `IHttpClientFactory` 后，就无需再使用此模式。

## <a name="logging"></a>日志记录

通过 `IHttpClientFactory` 创建的客户端记录所有请求的日志消息。 在日志记录配置中启用合适的信息级别可以查看默认日志消息。 仅在跟踪级别包含附加日志记录（例如请求标头的日志记录）。

用于每个客户端的日志类别包含客户端名称。 例如，名为“MyNamedClient”的客户端使用 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 类别来记录消息。 后缀为 LogicalHandler 的消息在请求处理程序管道外部发生。 在请求时，在管道中的任何其他处理程序处理请求之前记录消息。 在响应时，在任何其他管道处理程序接收响应之后记录消息。

日志记录还在请求处理程序管道内部发生。 在“MyNamedClient”示例中，这些消息是针对日志类别 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 进行记录。 在请求时，在所有其他处理程序运行后，以及刚好在通过网络发出请求之前记录消息。 在响应时，此日志记录包含响应在通过处理程序管道被传递回去之前的状态。

在管道内外启用日志记录，可以检查其他管道处理程序做出的更改。 例如，其中可能包含对请求标头的更改，或者对响应状态代码的更改。

通过在日志类别中包含客户端名称，可以在必要时对特定的命名客户端筛选日志。

## <a name="configure-the-httpmessagehandler"></a>配置 HttpMessageHandler

控制客户端使用的内部 `HttpMessageHandler` 的配置是有必要的。

在添加命名客户端或类型化客户端时，会返回 `IHttpClientBuilder`。 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 扩展方法可以用于定义委托。 委托用于创建和配置客户端使用的主要 `HttpMessageHandler`：

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]
