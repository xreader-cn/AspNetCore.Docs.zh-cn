---
title: 在浏览器应用中使用 gRPC
author: jamesnk
description: 了解如何在 ASP.NET Core 上配置 gRPC 服务，以便从使用 gRPC 的浏览器应用程序调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172274"
---
# <a name="use-grpc-in-browser-apps"></a>在浏览器应用中使用 gRPC

按[James 牛顿-k](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-.NET 中的 Web 支持是试验性的**
>
> gRPC-Web for .NET 是一个试验性项目，而不是已提交的产品。 我们想要：
>
> * 测试实现 gRPC 的方法。
> * 如果对 .NET 开发人员而言，此方法对 .NET 开发人员非常有用，这种方法与通过代理设置 gRPC 的传统方法有关。
>
> 请在[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)上留下反馈，以确保我们的开发人员喜欢和的工作效率。

不能从基于浏览器的应用程序中调用 HTTP/2 gRPC 服务。 [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)是一种允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务的协议。 本文介绍如何在 .NET Core 中使用 gRPC Web。

## <a name="configure-grpc-web-in-aspnet-core"></a>在 ASP.NET Core 中配置 gRPC-Web

ASP.NET Core 中托管的 gRPC services 可配置为支持 gRPC 和 HTTP/2 gRPC。 gRPC-Web 不需要对服务进行任何更改。 唯一的修改是启动配置。

使用 ASP.NET Core gRPC 服务启用 gRPC-Web：

* 添加对[Grpc](https://www.nuget.org/packages/Grpc.AspNetCore.Web)包的引用。
* 将应用程序配置为使用 gRPC，方法是将 `AddGrpcWeb` 和 `UseGrpcWeb` 添加到*Startup.cs*：

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

前面的代码：

* 在路由之后和终结点之前添加 gRPC-Web 中间件、`UseGrpcWeb`。
* 指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持具有 `EnableGrpcWeb`的 gRPC Web。 

或者，通过将 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` 添加到 ConfigureServices，将所有服务配置为支持 gRPC。

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

可能需要一些其他配置才能从浏览器调用 gRPC，例如配置 ASP.NET Core 以支持 CORS。 有关详细信息，请参阅[支持 CORS](xref:security/cors)。

## <a name="call-grpc-web-from-the-browser"></a>从浏览器调用 gRPC-Web

浏览器应用可以使用 gRPC 来调用 gRPC 服务。 通过浏览器从 gRPC 调用 gRPC 服务时，有一些要求和限制：

* 服务器必须配置为支持 gRPC。
* 不支持客户端流式处理和双向流式处理调用。 支持服务器流式处理。
* 若要在不同的域上调用 gRPC 服务，需要在服务器上配置[CORS](xref:security/cors) 。

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web 客户端

存在 JavaScript gRPC Web 客户端。 有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>通过 .NET gRPC 客户端配置 gRPC-Web

可以将 .NET gRPC 客户端配置为进行 gRPC 调用。 这对于在浏览器中承载的[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)应用程序非常有用，并且具有与 JavaScript 代码相同的 HTTP 限制。 使用 .NET 客户端调用 gRPC 与[HTTP/2 gRPC 相同](xref:grpc/client)。 唯一的修改就是创建通道的方式。

使用 gRPC：

* 添加对[Grpc](https://www.nuget.org/packages/Grpc.Net.Client.Web)包的引用。
* 请确保对[Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client)的引用2.27.0 或更高版本。
* 将通道配置为使用 `GrpcWebHandler`：

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

前面的代码：

* 将通道配置为使用 gRPC。
* 创建一个客户端，并使用通道发出调用。

创建时，`GrpcWebHandler` 具有以下配置选项：

* **InnerHandler**：使 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler> 例如，`HttpClientHandler`。
* **Mode**：指定是否 `application/grpc-web` 或 `application/grpc-web-text``Content-Type` gRPC HTTP 请求请求的枚举类型。
    * `GrpcWebMode.GrpcWeb` 配置在不进行编码的情况下发送的内容。 默认值。
    * `GrpcWebMode.GrpcWebText` 将内容配置为 base64 编码。 对于浏览器中的服务器流式处理调用是必需的。
* **HttpVersion**： http 协议 `Version` 用于在基础 gRPC HTTP 请求上设置[HttpRequestMessage。](xref:System.Net.Http.HttpRequestMessage.Version) gRPC-Web 不需要特定版本，并且除非指定，否则不会覆盖默认版本。

> [!IMPORTANT]
> 生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。 例如，`SayHello` 为 sync，`SayHelloAsync` 为 async。 在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。 异步方法必须始终在 Blazor WebAssembly 中使用。

## <a name="additional-resources"></a>其他资源

* [Web 客户端 GitHub 项目的 gRPC](https://github.com/grpc/grpc-web)
* <xref:security/cors>
