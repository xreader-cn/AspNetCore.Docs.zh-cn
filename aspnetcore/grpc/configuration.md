---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/30/2019
uid: grpc/configuration
ms.openlocfilehash: e269d701f45c0b852a9006107f0162cc5af2c38a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814928"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 配置的 gRPC

## <a name="configure-services-options"></a>配置服务选项

下表描述了用于配置 gRPC 服务的选项：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | 可以从服务器发送的字节数最大消息大小。 尝试发送一条消息，超出了配置的最大消息大小会引发异常。 |
| `ReceiveMaxMessageSize` | 4 MB | 服务器可以接收的字节数最大消息大小。 如果服务器收到消息超出此限制时，它将引发异常。 增加此值允许服务器以接收更大的消息，但会降低内存占用情况。 |
| `EnableDetailedErrors` | `false` | 如果`true`、 详细的异常消息返回到客户端，在服务方法中引发异常时。 默认值为 `false`。 设置`EnableDetailedErrors`到`true`可能会泄漏敏感信息。 |
| `CompressionProviders` | gzip | 压缩来压缩和解压缩消息的提供程序的集合。 可以创建自定义压缩提供程序，并将其添加到集合。 配置提供程序支持的默认值**gzip**压缩。 |
| `ResponseCompressionAlgorithm` | `null` | 使用可压缩从服务器发送的消息的压缩算法。 该算法必须与匹配中的压缩提供程序`CompressionProviders`。 对于要压缩响应的算法，客户端必须指示它通过发送支持该算法**grpc 接受编码**标头。 |
| `ResponseCompressionLevel` | `null` | 使用可压缩从服务器发送的消息的压缩级别。 |

可以通过提供一个选项委托到的所有服务配置选项`AddGrpc`调用中`Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

单个服务的选项重写中提供的全局选项`AddGrpc`，可以使用配置`AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>配置客户端选项

下面的代码设置客户端最大发送和接收消息大小：

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
