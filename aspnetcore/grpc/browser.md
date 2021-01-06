---
title: 在浏览器应用中使用 gRPC
author: jamesnk
description: 了解如何配置 ASP.NET Core 上的 gRPC 服务，以使其可以从使用 gRPC-Web 的浏览器应用中进行调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058904"
---
# <a name="use-grpc-in-browser-apps"></a>在浏览器应用中使用 gRPC

作者：[James Newton-King](https://twitter.com/jamesnk)

 了解如何配置现有 ASP.NET Core gRPC 服务，以使其可以从使用 [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 协议的浏览器应用中进行调用。 gRPC-Web 允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务。 无法从基于浏览器的应用中调用 HTTP/2 gRPC 服务。 可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。


有关将 gRPC 服务添加到现有 ASP.NET Core 应用的说明，请参阅[将 gRPC 服务添加到 ASP.NET Core 应用](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)。

有关创建 gRPC 项目的说明，请参阅 <xref:tutorials/grpc/grpc-start>。

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>.NET Core 中的 gRPC-Web 与Envoy

有两种方式可将 gRPC-Web 添加到 ASP.NET Core 应用中：

* 在 ASP.NET Core 中同时支持 gRPC-Web 和 gRPC HTTP/2。 此选项会使用 `Grpc.AspNetCore.Web` 包提供的中间件。
* 使用 [Envoy 代理](https://www.envoyproxy.io/)的 gRPC-Web 支持将 gRPC-Web 转换为 gRPC HTTP/2。 转换后的调用随后会转发给 ASP.NET Core 应用。

每种方法既有优点，也有缺点。 如果应用的环境已将 Envoy 用作代理，则也可以使用 Envoy 提供 gRPC-Web 支持。 对于仅需要 ASP.NET Core 的 gRPC-Web 的基本解决方案，`Grpc.AspNetCore.Web` 是一个不错的选择。

## <a name="configure-grpc-web-in-aspnet-core"></a>配置 ASP.NET Core 中的 gRPC-Web

可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。 gRPC-Web 不需要对服务进行任何更改。 唯一的修改是启动配置。

若要使用 ASP.NET Core gRPC 服务启用 gRPC-Web：

* 添加对 [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 包的引用。
* 配置应用以使用 gRPC-Web，方法是将 `UseGrpcWeb` 和 `EnableGrpcWeb` 添加到 Startup.cs：

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

前面的代码：

* 在路由之后、终结点之前添加 gRPC-Web 中间件 `UseGrpcWeb`。
* 指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `EnableGrpcWeb` 的 gRPC-Web。 

或者，可以配置 gRPC-Web 中间件，使所有服务在默认情况下都支持 gRPC-Web，而不需要 `EnableGrpcWeb`。 在添加中间件时指定 `new GrpcWebOptions { DefaultEnabled = true }`。

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> 存在一个已知问题，它导致在 .NET Core 3.x 中[由 Http.sys 托管](xref:fundamentals/servers/httpsys)时 gRPC-Web 会失败。
>
> [此处](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)提供了一种变通方法，可用来获取在 Http.sys 上工作 gRPC-Web。

### <a name="grpc-web-and-cors"></a>gRPC-Web 和 CORS

浏览器安全性可防止网页向不处理网页的域发送请求。 此限制适用于使用浏览器应用发出 gRPC-Web 调用。 例如，由 `https://www.contoso.com` 提供服务的浏览器应用对托管于 `https://services.contoso.com` 上的 gRPC-Web 服务的调用会被阻止。 跨域资源共享 (CORS) 可用于放宽此限制。

若要允许浏览器应用进行跨域 gRPC-Web 调用，请[在 ASP.NET Core 中设置 CORS](xref:security/cors)。 使用内置 CORS 支持，并使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> 公开特定于 gRPC 的标头。

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

前面的代码：

* 调用 `AddCors` 以添加 CORS 服务，并配置公开特定于 gRPC 的标头的 CORS 策略。
* 调用 `UseCors` 以在路由之后、终结点之前添加 CORS 中间件。
* 指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `RequiresCors`的 CORS。

### <a name="grpc-web-and-streaming"></a>gRPC-Web 和流式处理

HTTP/2 上的传统 gRPC 支持所有方向的流式处理。 gRPC-Web 对流式处理提供有限的支持：

* gRPC-Web 浏览器客户端不支持调用客户端流式处理和双向流式处理方法。
* 托管在 Azure 应用服务和 IIS 上的 ASP.NET Core gRPC 服务不支持双向流式处理。

使用 gRPC 时，仅建议使用一元方法和服务器流式处理方法。

## <a name="call-grpc-web-from-the-browser"></a>从浏览器调用 gRPC-Web

浏览器应用可以使用 gRPC-Web 来调用 gRPC 服务。 使用 gRPC-Web 从浏览器调用 gRPC 服务时，存在一些要求和限制：

* 服务器必须已配置为支持 gRPC-Web。
* 不支持客户端流式处理和双向流式处理调用。 支持服务器流式处理。
* 在其他域上调用 gRPC 服务需要在服务器上配置 [CORS](xref:security/cors)。

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web 客户端

存在 JavaScript gRPC-Web 客户端。 有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC-Web 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>使用 .NET gRPC 客户端配置 gRPC-Web

可以将 .NET gRPC 客户端配置为发出 gRPC-Web 调用。 这对于托管在浏览器中且具有相同 JavaScript 代码 HTTP 限制的 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 应用来说非常有用。 使用 .NET 客户端调用 gRPC-Web [与 HTTP/2 gRPC 相同](xref:grpc/client)。 唯一的修改是创建通道的方式。

若要使用 gRPC-Web：

* 添加对 [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 包的引用。
* 确保对 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 包的引用为 2.29.0 或更高版本。
* 配置通道以使用 `GrpcWebHandler`：

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

前面的代码：

* 配置通道以使用 gRPC-Web。
* 创建一个客户端并使用通道发出调用。

`GrpcWebHandler` 具有以下配置选项：

* **InnerHandler**：发出 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。
* GrpcWebMode：枚举类型，指定 gRPC HTTP 请求 `Content-Type` 是 `application/grpc-web` 还是 `application/grpc-web-text`。
    * `GrpcWebMode.GrpcWeb` 配置不进行编码即发送的内容。 默认值。
    * `GrpcWebMode.GrpcWebText` 配置需进行 base64 编码的内容。 对于浏览器中的服务器流式处理调用是必需的。
* **HttpVersion**：HTTP 协议 `Version` 用于在基础 gRPC HTTP 请求上设置 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)。 gRPC-Web 不需要特定版本，且除非指定，否则不会替代默认版本。

> [!IMPORTANT]
> 生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。 例如，`SayHello` 是同步的，而 `SayHelloAsync` 是异步的。 在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。 必须始终在 Blazor WebAssembly 中使用异步方法。

### <a name="use-grpc-client-factory-with-grpc-web"></a>将 gRPC-Web 与 gRPC 客户端工厂一起使用

可以使用 gRPC 与 [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory) 的集成来创建与 gRPC-Web 兼容的 .NET 客户端。

若要将 gRPC-Web 与客户端工厂一起使用，请执行以下操作：

* 将包引用添加到以下包的项目文件中：
  * [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* 使用泛型 `AddGrpcClient` 扩展方法，通过依赖项注入 (DI) 注册 gRPC 客户端。 在 Blazor WebAssembly 应用中，服务在 `Program.cs` 中通过 DI 注册。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 扩展方法配置 `GrpcWebHandler`。

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

有关详细信息，请参阅 <xref:grpc/clientfactory>。

## <a name="additional-resources"></a>其他资源

* [适用于 Web 客户端 GitHub 项目的 gRPC](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
