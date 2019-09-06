---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何为 ASP.NET Core 应用配置 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310319"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 配置的 gRPC

## <a name="configure-services-options"></a>配置服务选项

下表描述了用于配置 gRPC 服务的选项：

| 选项 | Default Value | 描述 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | 可从服务器发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息将导致异常。 |
| `MaxReceiveMessageSize` | 4 MB | 服务器可接收的最大消息大小（以字节为单位）。 如果服务器收到的消息超过此限制，则会引发异常。 增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。 |
| `EnableDetailedErrors` | `false` | 如果`true`为，则在服务方法中引发异常时，详细的异常消息将返回到客户端。 默认值为 `false`。 设置`EnableDetailedErrors` 为`true`可以泄露敏感信息。 |
| `CompressionProviders` | gzip、deflate | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认配置的提供程序支持**gzip**和**deflate**压缩。 |
| `ResponseCompressionAlgorithm` | `null` | 压缩算法用于压缩从服务器发送的消息。 该算法必须与中`CompressionProviders`的压缩提供程序匹配。 为了使算法压缩响应，客户端必须通过在**grpc-accept**标头中发送来指示它支持该算法。 |
| `ResponseCompressionLevel` | `null` | 用于压缩从服务器发送的消息的压缩级别。 |

可以通过在中`AddGrpc` `Startup.ConfigureServices`提供调用的选项委托，为所有服务配置选项：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

单个服务的选项可替代和中`AddGrpc`提供的全局选项，可以使用`AddServiceOptions<TService>`以下方法配置：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>配置客户端选项

下表描述了用于配置 gRPC 通道的选项：

| 选项 | Default Value | 描述 |
| ------ | ------------- | ----------- |
| `HttpClient` | 新实例 | 用于`HttpClient`进行 gRPC 调用的。 可以将客户端设置为配置自定义`HttpClientHandler`，也可以将其他处理程序添加到 HTTP 管道进行 gRPC 调用。 默认情况下， `HttpClient`会创建一个新实例。 |
| `MaxSendMessageSize` | `null` | 可从客户端发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息将导致异常。 |
| `MaxReceiveMessageSize` | 4 MB | 客户端可以接收的最大消息大小（以字节为单位）。 如果服务器收到的消息超过此限制，则会引发异常。 增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。 |
| `TransportOptions` | `null` | 传输选项配置信道如何调用 gRPC 服务。 目前，唯一的实现`HttpClientTransport`是选项，用于指定`HttpClient` gRPC 使用的。 |
| `Credentials` | `null` | 一个 `ChannelCredentials` 实例。 凭据用于将身份验证元数据添加到 gRPC 调用。 |
| `CompressionProviders` | gzip、deflate | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认配置的提供程序支持**gzip**和**deflate**压缩。 |

下面的代码：

* 设置通道上的最大发送和接收消息大小。
* 创建客户端。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a>其他资源

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
