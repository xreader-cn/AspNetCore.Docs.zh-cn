---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041884"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 配置的 gRPC

## <a name="configure-services-options"></a>配置服务选项

下表描述了用于配置 gRPC 服务的选项：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | 可以从服务器发送的字节数最大消息大小。 尝试发送一条消息，超出了配置的最大消息大小会引发异常。 |
| `ReceiveMaxMessageSize` | 4 MB | 服务器可以接收的字节数最大消息大小。 如果服务器收到消息超出此限制时，它将引发异常。 增加此值允许服务器以接收更大的消息，但会降低内存占用情况。 |
| `EnableDetailedErrors` | `false` | 如果`true`、 详细的异常消息返回到客户端，在服务方法中引发异常时。 默认值为 `false`。 此值设置为`true`可能会泄漏敏感信息。 |
| `CompressionProviders` | gzip | 压缩来压缩和解压缩消息的提供程序的集合。 可以创建自定义压缩提供程序，并将其添加到集合。 配置提供程序支持的默认值**gzip**压缩。 |
| `ResponseCompressionAlgorithm` | `null` | 使用可压缩从服务器发送的消息的压缩算法。 该算法必须与匹配中的压缩提供程序`CompressionProviders`。 对于 algorthm 压缩响应客户端必须指示它通过发送支持该算法**grpc 接受编码**标头。 |
| `ResponseCompressionLevel` | `null` | 使用可压缩从服务器发送的消息的压缩级别。 |

可以通过提供一个选项委托到的所有服务配置选项`AddGrpc`调用中`Startup.ConfigureServices`。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

单个服务的选项重写中提供的全局选项`AddGrpc`，可以使用配置`AddServiceOptions<TService>`:

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
