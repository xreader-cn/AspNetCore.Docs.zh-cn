---
title: 将 gRPC services 从 C 核迁移到 ASP.NET Core
author: juntaoluo
description: 了解如何将现有的基于 C 核的 gRPC 应用移到 ASP.NET Core 堆栈顶部。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 596eca0f510387a18472eb353672980e0a8e0d24
ms.sourcegitcommit: eb4fcdeb2f9e8413117624de42841a4997d1d82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697996"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>将 gRPC services 从 C 核迁移到 ASP.NET Core

作者：[John Luo](https://github.com/juntaoluo)

由于底层堆栈的实现，并非所有功能在[基于 C 核的 gRPC](https://grpc.io/blog/grpc-stacks)应用与基于 ASP.NET Core 的应用程序之间的工作方式相同。 本文档重点介绍两个堆栈之间的迁移的主要区别。

## <a name="grpc-service-implementation-lifetime"></a>gRPC 服务实现生存期

在 ASP.NET Core 堆栈中，默认情况下，使用范围内的[生存期](xref:fundamentals/dependency-injection#service-lifetimes)创建 gRPC services。 与此相反，默认情况下 gRPC C 核心将绑定到[单一实例生存期内](xref:fundamentals/dependency-injection#service-lifetimes)的服务。

范围内的生存期允许服务实现使用范围内的生存期解析其他服务。 例如，作用域内的生存期还可以通过构造函数注入解析 DI 容器 `DbContext`。 使用范围生存期：

* 为每个请求构造服务实现的新实例。
* 不能通过实现类型上的实例成员来共享请求之间的状态。
* 预期是在 DI 容器的单一实例服务中存储共享状态。 在 gRPC 服务实现的构造函数中解析存储的共享状态。

有关服务生存期的详细信息，请参阅 <xref:fundamentals/dependency-injection#service-lifetimes>。

### <a name="add-a-singleton-service"></a>添加单一实例服务

为了促进从 gRPC 的 C 核心实现到 ASP.NET Core 的转换，可以将服务实现的服务生存期从作用域改为单一实例。 这涉及到将服务实现的实例添加到 DI 容器：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

但是，具有单一生存期的服务实现不再能够通过构造函数注入来解析范围内的服务。

## <a name="configure-grpc-services-options"></a>配置 gRPC services 选项

在基于 C 的应用程序中，当[构造服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)时，将在 `ChannelOption` 配置 `grpc.max_receive_message_length` 和 `grpc.max_send_message_length` 等设置。

在 ASP.NET Core 中，gRPC 通过 `GrpcServiceOptions` 类型提供配置。 例如，可以通过 `AddGrpc` 配置 gRPC 服务的最大传入消息大小。 下面的示例将默认 `MaxReceiveMessageSize` 为 4 MB 到 16 MB：

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

基于 C 核的应用依赖于 `GrpcEnvironment` 来[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)，以便进行调试。 ASP.NET Core 堆栈通过[日志记录 API](xref:fundamentals/logging/index)提供此功能。 例如，可以通过构造函数注入将记录器添加到 gRPC 服务：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

基于 C 核的应用通过[服务器端口属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)配置 HTTPS。 类似的概念用于在 ASP.NET Core 中配置服务器。 例如，Kestrel 使用[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)来实现此功能。

## <a name="interceptors-and-middleware"></a>拦截和中间件

与基于 gRPC 的应用中的侦听器相比，[中间件](xref:fundamentals/middleware/index)提供的功能类似。 ASP.NET Core 中间件和侦听器在概念上是相同的，它们用于构造处理 gRPC 请求的管道。 它们都允许在管道中的下一个组件之前或之后执行工作。 但 ASP.NET Core 中间件对基础 HTTP/2 消息进行操作，而侦听器则使用[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)在 gRPC 抽象层上操作。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
