---
title: 使用 .NET 的代码优先 gRPC 服务和客户端
author: jamesnk
description: 了解使用 .NET 编写代码优先 gRPC 的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880616"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>使用 .NET 的代码优先 gRPC 服务和客户端

作者：[James Newton-King](https://twitter.com/jamesnk) 和 [Marc Gravell](https://twitter.com/marcgravell)

代码优先 gRPC 使用 .NET 类型来定义服务和消息协定。

当整个系统使用 .NET 时，代码优先是一个不错的选择：

* 可以在 .NET 服务器和客户端之间共享 .NET 服务和数据协定类型。
* 无需在 `.proto` 文件和代码生成过程中定义协定。

不建议在具有多种语言的 polyglot 系统中使用代码优先。 .NET 服务和数据协定类型不能与非 .NET 平台一起使用。 其他平台若要调用使用代码优先编写的 gRPC 服务，必须创建一个与该服务匹配的 `.proto` 协定。

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> 有关 protobuf-net.Grpc 的帮助信息，请访问 [protobuf-net.Grpc 网站](https://protobuf-net.github.io/protobuf-net.Grpc/)或在 [protobuf-net.Grpc GitHub 存储库](https://github.com/protobuf-net/protobuf-net.Grpc)上创建一个问题。

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) 是一个社区项目，不受 Microsoft 支持。 它将对 `Grpc.AspNetCore` 和 `Grpc.Net.Client` 添加代码优先支持。 它使用通过属性批注的 .NET 类型来定义应用的 gRPC 服务和消息。

创建代码优先 gRPC 服务的第一步是定义代码协定：

* 创建一个将由服务器和客户端共享的新项目。
* 添加一个 [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 包引用。
* 创建服务和数据协定类型。

[!code-csharp[](code-first/Contracts.cs)]

前面的代码：

* 定义 `HelloRequest` 和 `HelloReply` 消息。
* 使用一元 `SayHelloAsync` gRPC 方法定义 `IGreeterService` 协定接口。

服务协定在服务器上实现并从客户端调用。 在服务接口上定义的方法必须与某些签名匹配，具体取决于它们是一元、服务器流式处理、客户端流式处理还是双向流式处理。

有关定义服务协定的详细信息，请参阅 [protobuf-net.Grpc 入门文档](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)。

## <a name="create-a-code-first-grpc-service"></a>创建代码优先 gRPC 服务

若要将 gRPC 代码优先服务添加到 ASP.NET Core 应用，请执行以下步骤：

* 添加一个 [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 包引用。
* 添加一个对共享代码协定项目的引用。

创建一个新的 `GreeterService.cs` 文件并实现 `IGreeterService` 服务接口：

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

更新 `Startup.cs` 文件：

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

在上述代码中：

* `AddCodeFirstGrpc` 注册启用了代码优先的服务。
* `MapGrpcService<GreeterService>` 添加代码优先的服务终结点。

使用代码优先和 `.proto` 文件实现的 gRPC 服务可在同一应用中共存。 所有 gRPC 服务都使用 [gRPC 服务配置](xref:grpc/configuration#configure-services-options)。

## <a name="create-a-code-first-grpc-client"></a>创建代码优先 gRPC 客户端

代码优先 gRPC 客户端使用服务协定来调用 gRPC 服务。 若要使用代码优先客户端调用 gRPC 服务，请执行以下步骤：

* 添加一个 [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 包引用。
* 添加一个对共享代码协定项目的引用。

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

前面的代码：

* 创建一个 gRPC 通道。
* 使用 `CreateGrpcService<IGreeterService>` 扩展方法从通道创建代码优先客户端。
* 使用 `SayHelloAsync` 调用 gRPC 服务。

代码优先 gRPC 客户端是通过通道创建的。 与常规客户端一样，代码优先客户端使用其[通道配置](xref:grpc/configuration#configure-client-options)。

## <a name="additional-resources"></a>其他资源

* [protobuf-net.Grpc 网站](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net.Grpc GitHub 存储库](https://github.com/protobuf-net/protobuf-net.Grpc)
