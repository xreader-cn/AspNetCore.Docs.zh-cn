---
title: 将 gRPC 服务从 C-core 迁移到 ASP.NET Core
author: juntaoluo
description: 了解如何移动现有的基于 C-core 的 gRPC 应用，以在 ASP.NET Core 堆栈之上运行。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 27c53dd4b41d6c99e45fccb5af79bab1ed5dc1b9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253145"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>将 gRPC 服务从 C-core 迁移到 ASP.NET Core

作者：[John Luo](https://github.com/juntaoluo)

由于基础堆栈的实现，不是所有功能在[基于 C-core 的 gRPC](https://grpc.io/blog/grpc-stacks) 应用和基于 ASP.NET Core 的应用之间都以相同的方式工作。 本文档重点介绍在两个堆栈之间迁移的主要区别。

## <a name="grpc-service-implementation-lifetime"></a>gRPC 服务实现生存期

在 ASP.NET Core 堆栈中，默认情况下，创建的 gRPC 服务具有[范围内的生存期](xref:fundamentals/dependency-injection#service-lifetimes)。 与此相反，默认情况下，gRPC C-core 会绑定到具有[单一实例生存期](xref:fundamentals/dependency-injection#service-lifetimes)的服务。

范围内的生存期允许服务实现解析具有范围内的生存期的其他服务。 例如，范围内的生存期还可以通过构造函数注入解析 DI 容器中的 `DbContext`。 使用范围内的生存期：

* 为每个请求构造服务实现的新实例。
* 不能通过实现类型上的实例成员在请求之间共享状态。
* 预期是在 DI 容器的单一实例服务中存储共享状态。 在 gRPC 服务实现的构造函数中解析存储的共享状态。

有关服务生存期的详细信息，请参阅 <xref:fundamentals/dependency-injection#service-lifetimes>。

### <a name="add-a-singleton-service"></a>添加单一实例服务

为了促进从 gRPC C-core 实现到 ASP.NET Core 的转换，可以将服务实现的服务生存期从范围内改为单一实例。 这涉及到将服务实现的实例添加到 DI 容器：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

但是，具有单一实例生存期的服务实现不再能够通过构造函数注入来解析范围内的服务。

## <a name="configure-grpc-services-options"></a>配置 gRPC 服务选项

在基于 C-core 的应用中，[构造服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)时，使用 `ChannelOption` 配置 `grpc.max_receive_message_length` 和 `grpc.max_send_message_length` 等设置。

在 ASP.NET Core 中，gRPC 通过 `GrpcServiceOptions` 类型提供配置。 例如，可以通过 `AddGrpc` 配置 gRPC 服务的最大传入消息大小。 下面的示例将默认 `MaxReceiveMessageSize` 4 MB 更改为 16 MB：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

有关配置的详细信息，请参阅 <xref:grpc/configuration>。

## <a name="logging"></a>Logging

基于 C-core 的应用依赖于 `GrpcEnvironment` 来[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)以进行调试。 ASP.NET Core 堆栈通过 [Logging API](xref:fundamentals/logging/index) 提供此功能。 例如，可以通过构造函数注入将记录器添加到 gRPC 服务：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

::: moniker range=">= aspnetcore-5.0"
基于 C-core 的应用通过 [ 属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)配置 HTTPS。 类似的概念用于在 ASP.NET Core 中配置服务器。 例如，Kestrel 将[终结点配置](xref:fundamentals/servers/kestrel/endpoints)用于此目的。
::: moniker-end

::: moniker range="< aspnetcore-5.0"
基于 C-core 的应用通过 [ 属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)配置 HTTPS。 类似的概念用于在 ASP.NET Core 中配置服务器。 例如，Kestrel 将[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)用于此目的。
::: moniker-end

## <a name="grpc-interceptors-vs-middleware"></a>gRPC 拦截器与中间件

与基于 C-core 的 gRPC 应用中的拦截器相比，ASP.NET Core [中间件](xref:fundamentals/middleware/index)提供类似功能。 ASP.NET Core 中间件和拦截器在概念上类似。 两者：

* 用于构造处理 gRPC 请求的管道。
* 允许在管道中的下一个组件前或后执行工作。
* 提供对 `HttpContext` 的访问权限：
  * 在中间件中，`HttpContext` 是参数。
  * 在拦截器中，可以通过 `ServerCallContext.GetHttpContext` 扩展方法使用 `ServerCallContext` 参数访问 `HttpContext`。 请注意，此功能特定于在 ASP.NET Core 中运行的拦截器。

gRPC 拦截器与 ASP.NET Core 中间件的不同之处：

* 拦截器：
  * 使用 [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html) 在 gRPC 抽象层上操作。
  * 提供对以下内容的访问权限：
    * 发送到调用的反序列化消息。
    * 序列化之前从调用返回的消息。
  * 可以捕获和处理从 gRPC 服务引发的异常。
* 中间件：
  * 在 gRPC 拦截器之前运行。
  * 对基础 HTTP/2 消息进行操作。
  * 只能访问请求和响应流中的字节。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
