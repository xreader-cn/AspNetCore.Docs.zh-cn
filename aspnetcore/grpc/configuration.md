---
title: gRPC for .NET 配置
author: jamesnk
description: 了解如何配置适用于 .NET 应用的 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355734"
---
# <a name="grpc-for-net-configuration"></a>gRPC for .NET 配置

## <a name="configure-services-options"></a>配置服务选项

在*Startup.cs*中 `AddGrpc` 配置 gRPC 服务。 下表描述了用于配置 gRPC 服务的选项：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | 可从服务器发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息将导致异常。 |
| `MaxReceiveMessageSize` | 4 MB | 服务器可接收的最大消息大小（以字节为单位）。 如果服务器收到的消息超过此限制，则会引发异常。 增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。 |
| `EnableDetailedErrors` | `false` | 如果 `true`，则当服务方法中引发异常时，详细的异常消息将返回到客户端。 默认值为 `false`。 将 `EnableDetailedErrors` 设置为 `true` 可能会泄漏敏感信息。 |
| `CompressionProviders` | gzip | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认配置的提供程序支持**gzip**压缩。 |
| `ResponseCompressionAlgorithm` | `null` | 压缩算法用于压缩从服务器发送的消息。 该算法必须与 `CompressionProviders`中的压缩提供程序匹配。 为了使算法压缩响应，客户端必须通过在**grpc-accept**标头中发送来指示它支持该算法。 |
| `ResponseCompressionLevel` | `null` | 用于压缩从服务器发送的消息的压缩级别。 |
| `Interceptors` | 无 | 运行每个 gRPC 调用的侦听器的集合。 拦截按注册顺序运行。 全局配置的侦听器在为单个服务配置侦听器之前运行。 有关 gRPC 侦听器的详细信息，请参阅[GRPC 侦听器和中间件](xref:grpc/migration#grpc-interceptors-vs-middleware)。 |

可以通过向 `Startup.ConfigureServices`中的 `AddGrpc` 调用提供选项委托，为所有服务配置选项：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

单个服务的选项将覆盖 `AddGrpc` 中提供的全局选项，并且可以使用 `AddServiceOptions<TService>`进行配置：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>配置客户端选项

在 `GrpcChannelOptions`上设置 gRPC 客户端配置。 下表描述了用于配置 gRPC 通道的选项：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `HttpClient` | 新实例 | 用于进行 gRPC 调用的 `HttpClient`。 可以将客户端设置为配置自定义 `HttpClientHandler`，或将其他处理程序添加到 gRPC 调用的 HTTP 管道。 如果未指定 `HttpClient`，则将为该通道创建新的 `HttpClient` 实例。 它将自动被释放。 |
| `DisposeHttpClient` | `false` | 如果 `true`并且指定了 `HttpClient`，则在释放 `GrpcChannel` 时将释放 `HttpClient` 实例。 |
| `LoggerFactory` | `null` | 客户端用来记录有关 gRPC 调用的信息的 `LoggerFactory`。 可以通过依赖关系注入或使用 `LoggerFactory.Create`创建 `LoggerFactory` 实例。 有关配置日志记录的示例，请参阅 <xref:grpc/diagnostics#grpc-client-logging>。 |
| `MaxSendMessageSize` | `null` | 可从客户端发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息将导致异常。 |
| `MaxReceiveMessageSize` | 4 MB | 客户端可以接收的最大消息大小（以字节为单位）。 如果客户端收到的消息超过此限制，则会引发异常。 增大此值可使客户端接收更大的消息，但会对内存消耗产生负面影响。 |
| `Credentials` | `null` | 一个 `ChannelCredentials` 实例。 凭据用于将身份验证元数据添加到 gRPC 调用。 |
| `CompressionProviders` | gzip | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认配置的提供程序支持**gzip**压缩。 |

下面的代码：

* 设置通道上的最大发送和接收消息大小。
* 创建客户端。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>其他资源

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
