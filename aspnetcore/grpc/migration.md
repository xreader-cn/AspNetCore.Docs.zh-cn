---
title: GRPC 服务迁移从 C core 到 ASP.NET Core
author: juntaoluo
description: 了解如何将移动现有的 C-core 基于的 gRPC 应用运行 ASP.NET Core 堆栈顶部。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 4d489b5aecf2e15fbbe3ac472b991a4365cd47c1
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672614"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>GRPC 服务迁移从 C core 到 ASP.NET Core

作者：[John Luo](https://github.com/juntaoluo)

由于基础堆栈实现，而不是所有功能的都工作方式之间相同[基于 C 内核 gRPC](https://grpc.io/blog/grpc-stacks)应用和基于 ASP.NET Core 的应用。 本文档重点介绍两个堆栈之间迁移的主要的差异。

## <a name="grpc-service-implementation-lifetime"></a>gRPC 服务实现生存期

在 ASP.NET Core 堆栈中，gRPC 服务，默认情况下，会创建与[作用域的生存期](xref:fundamentals/dependency-injection#service-lifetimes)。 与此相反，默认情况下的 C-核心 gRPC 将绑定到服务，具有[单一实例生存期](xref:fundamentals/dependency-injection#service-lifetimes)。

指定了作用域的生存期，若要解决限定了作用域生存期与其他服务的服务实现。 例如，作用域的生存期，也可以解决`DBContext`从 DI 容器可以通过构造函数注入。 使用作用域的生存期：

* 服务实现的新实例会为每个请求构造。
* 它不能通过上实现类型的实例成员发出的请求之间共享状态。
* 预期结果是要存储在 DI 容器中的单一实例服务中共享的状态。 GRPC 服务实现的构造函数中解析存储共享的状态。

服务生存期的详细信息，请参阅<xref:fundamentals/dependency-injection#service-lifetimes>。

### <a name="add-a-singleton-service"></a>添加单独的服务

为了便于从 gRPC C core 实现到 ASP.NET Core 的转换，则可以更改服务实现的服务生存期作用域为单一实例。 这涉及到将服务实现的实例添加到 DI 容器：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

但是，单一实例生存期的服务实现将不再能够解析通过构造函数注入的作用域的服务。

## <a name="configure-grpc-services-options"></a>配置 gRPC 服务选项

在基于 C 的核心应用中，设置，例如`grpc.max_receive_message_length`并`grpc.max_send_message_length`配置了`ChannelOption`时[构造的服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)。

在 ASP.NET Core 中，`GrpcServiceOptions`使您能够配置这些设置。 对所有 gRPC 服务或单个服务实现类型，可以全局应用的设置。 为单个服务实现类型指定的选项重写时配置的全局设置。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddGrpc(globalOptions =>
        {
            // Global settings
            globalOptions.SendMaxMessageSize = 4096
            globalOptions.ReceiveMaxMessageSize = 4096
        })
        .AddServiceOptions<GreeterService>(greeterOptions =>
        {
            // GreeterService settings. These will override global settings
            globalOptions.SendMaxMessageSize = 2048
            globalOptions.ReceiveMaxMessageSize = 2048
        })
}
```

## <a name="logging"></a>日志记录

基于 C 的核心应用程序依赖`GrpcEnvironment`到[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)出于调试目的。 ASP.NET Core 堆栈提供此功能通过[日志记录 API](xref:fundamentals/logging/index)。 例如，一个记录器可以添加到通过构造函数注入 gRPC 服务：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

基于 C 的核心应用程序配置通过 HTTPS [Server.Ports 属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)。 类似的概念用于在 ASP.NET Core 中配置服务器。 例如，使用 Kestrel[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)实现此功能。

## <a name="interceptors-and-middleware"></a>拦截程序和中间件

ASP.NET Core[中间件](xref:fundamentals/middleware/index)提供类似功能相比 C core 基于 gRPC 应用中的侦听器。 中间件和侦听器是从概念上讲相同的因为二者都用于构造处理 gRPC 请求管道。 它们都可用于在管道中的下一个组件前后执行工作。 但是，ASP.NET Core 中间件运行，在基础 HTTP/2 消息，而拦截器对使用抽象的 gRPC 层[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
