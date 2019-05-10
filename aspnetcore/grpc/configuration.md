---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087354"
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

## <a name="configure-kestrel-options"></a>配置 Kestrel 选项

Kestrel 服务器有影响 for ASP.NET gRPC 的行为的配置选项。

### <a name="request-body-data-rate-limit"></a>请求正文数据速率限制

默认情况下，Kestrel 服务器施加[最小请求正文数据速率](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>)。 有关流式处理客户端和双工流式处理的调用，可能不满足此速率和连接可能超时。最小值请求的正文 gRPC 服务包含流式处理客户端和双工流式处理调用时，必须禁用数据速率限制：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
