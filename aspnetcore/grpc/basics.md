---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 了解使用 C# 编写 gRPC 服务时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774751"
---
# <a name="grpc-services-with-c"></a>使用 C\# 的 gRPC 服务

本文档概述在 C# 中编写 [gRPC](https://grpc.io/docs/guides/) 应用所需的概念。 此处涵盖的主题适用于基于 [C-core](https://grpc.io/blog/grpc-stacks) 和基于 ASP.NET Core 的 gRPC 应用。

## <a name="proto-file"></a>proto 文件

gRPC 使用协定优先方法进行 API 开发。 默认情况下，协议缓冲区 (protobuf) 用作接口设计语言 (IDL)。 \*.proto  文件包含：

* gRPC 服务的定义。
* 在客户端与服务器之间发送的消息。

有关 protobuf 文件的语法的详细信息，请参阅[官方文档 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)。

例如，请考虑[开始使用 gRPC 服务](xref:tutorials/grpc/grpc-start)中使用的 greet.proto  文件：

* 定义 `Greeter` 服务。
* `Greeter` 服务定义 `SayHello` 调用。
* `SayHello` 发送 `HelloRequest` 消息并接收 `HelloReply` 消息：

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>将 .proto 文件添加到 C\# 应用

通过将 \*.proto  文件添加到 `<Protobuf>` 项组中，可将该文件包含在项目中：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>.proto 文件的 C# 工具支持

需要工具包 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 才能从 \*.proto  文件生成 C# 资产。 生成的资产（文件）：

* 在每次生成项目时按需生成。
* 不会添加到项目中或是签入到源代码管理中。
* 是包含在 obj  目录中的生成工件。

服务器和客户端项目都需要此包。 `Grpc.AspNetCore` 元包中包含对 `Grpc.Tools` 的引用。 服务器项目可以使用 Visual Studio 中的包管理器或通过将 `<PackageReference>` 添加到项目文件来添加 `Grpc.AspNetCore`：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

客户端项目应直接引用 `Grpc.Tools` 以及使用 gRPC 客户端所需的其他包。 运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>生成的 C# 资产

工具包会生成表示在所包含 \*.proto  文件中定义的消息的 C# 类型。

对于服务器端资产，会生成抽象服务基类型。 基类型包含 .proto  文件中包含的所有 gRPC 调用的定义。 创建一个派生自此基类型并为 gRPC 调用实现逻辑的具体服务实现。 对于 `greet.proto`（前面所述的示例），会生成一个包含虚拟 `SayHello` 方法的抽象 `GreeterBase` 类型。 具体实现 `GreeterService` 会替代该方法，并实现处理 gRPC 调用的逻辑。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

对于客户端资产，会生成一个具体客户端类型。 .proto  文件中的 gRPC 调用会转换为具体类型中的方法，可以进行调用。 对于 `greet.proto`（前面所述的示例），会生成一个 `GreeterClient` 类型。 调用 `GreeterClient.SayHelloAsync` 以发起对服务器的 gRPC 调用。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

默认情况下，会为 `<Protobuf>` 项组中包含的每个 \*.proto  文件都生成服务器和客户端资产。 若要确保服务器项目中仅生成服务器资产，请将 `GrpcServices` 属性设置为 `Server`。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

同样，该属性在客户端项目中设置为 `Client`。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
