---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 编写使用 gRPC 服务时了解的基本概念C#。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 5a88bd0e9f789058b3606691c5ebd9a74325ac9b
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376348"
---
# <a name="grpc-services-with-c"></a>gRPC 服务与 C\#

本文档概述了用于编写所需的概念[gRPC](https://grpc.io/docs/guides/)中的应用C#。 此处介绍的主题应用于两个[C 核心](https://grpc.io/blog/grpc-stacks)-基于和 ASP.NET Core 基于 gRPC 应用。

## <a name="proto-file"></a>proto 文件

gRPC 使用 API 开发的约定优先方法。 默认情况下作为接口设计语言 (IDL) 使用协议缓冲区 (protobuf)。 *.Proto*文件包含：

* GRPC 服务的定义。
* 客户端和服务器之间发送的消息。

有关语法 protobuf 文件的详细信息，请参阅[官方文档 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)。

例如，考虑*greet.proto*文件中使用[gRPC 服务入门](xref:tutorials/grpc/grpc-start):

* 定义`Greeter`服务。
* `Greeter`服务定义`SayHello`调用。
* `SayHello` 将发送`HelloRequest`消息，并接收`HelloResponse`消息：

[!code-proto[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>将.proto 文件添加到 C\#应用

*.Proto*情况下将其添加到项目中包含文件`<Protobuf>`项组：

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## <a name="c-tooling-support-for-proto-files"></a>C#.Proto 文件的工具支持

工具程序包[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)生成所需C#中的资产 *.proto*文件。 将生成的资产 （文件）：

* 生成按需在每次生成项目。
* 不会添加到项目或签入源控件。
* 是生成项目中包含*obj*目录。

此包是所需的服务器和客户端项目。 `Grpc.Tools` 可以通过使用 Visual Studio 中的包管理器或添加添加`<PackageReference>`的项目文件：

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。

## <a name="generated-c-assets"></a>生成C#资产

工具包生成C#类型表示定义中所包含的消息 *.proto*文件。

对于服务器端的资产会生成抽象服务基类型。 基类型包含所有 gRPC 调用中包含的定义 *.proto*文件。 创建此基类型派生并实现 gRPC 调用逻辑的具体的服务实现。 有关`greet.proto`，该示例前面所述，一个抽象`GreeterBase`类型，它包含一个虚拟`SayHello`生成方法。 具体实现`GreeterService`重写方法，并实现处理 gRPC 调用逻辑。

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

对于客户端的资产会生成具体的客户端类型。 调用 gRPC *.proto*文件转换为具体类型，可以调用的方法。 有关`greet.proto`，该示例前面所述，为具体`GreeterClient`生成的类型。 调用`GreeterClient.SayHello`启动到服务器的 gRPC 调用。

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

默认情况下，为每个生成服务器和客户端资产 *.proto*文件中包含`<Protobuf>`项组。 若要确保只有服务器资产在服务器项目中生成`GrpcServices`属性设置为`Server`。

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

同样，该属性设置为`Client`在客户端项目中。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
