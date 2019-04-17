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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="efd13-103">GRPC 服务迁移从 C core 到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efd13-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="efd13-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="efd13-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="efd13-105">由于基础堆栈实现，而不是所有功能的都工作方式之间相同[基于 C 内核 gRPC](https://grpc.io/blog/grpc-stacks)应用和基于 ASP.NET Core 的应用。</span><span class="sxs-lookup"><span data-stu-id="efd13-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="efd13-106">本文档重点介绍两个堆栈之间迁移的主要的差异。</span><span class="sxs-lookup"><span data-stu-id="efd13-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="efd13-107">gRPC 服务实现生存期</span><span class="sxs-lookup"><span data-stu-id="efd13-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="efd13-108">在 ASP.NET Core 堆栈中，gRPC 服务，默认情况下，会创建与[作用域的生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="efd13-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="efd13-109">与此相反，默认情况下的 C-核心 gRPC 将绑定到服务，具有[单一实例生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="efd13-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="efd13-110">指定了作用域的生存期，若要解决限定了作用域生存期与其他服务的服务实现。</span><span class="sxs-lookup"><span data-stu-id="efd13-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="efd13-111">例如，作用域的生存期，也可以解决`DBContext`从 DI 容器可以通过构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="efd13-111">For example, a scoped lifetime can also resolve `DBContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="efd13-112">使用作用域的生存期：</span><span class="sxs-lookup"><span data-stu-id="efd13-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="efd13-113">服务实现的新实例会为每个请求构造。</span><span class="sxs-lookup"><span data-stu-id="efd13-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="efd13-114">它不能通过上实现类型的实例成员发出的请求之间共享状态。</span><span class="sxs-lookup"><span data-stu-id="efd13-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="efd13-115">预期结果是要存储在 DI 容器中的单一实例服务中共享的状态。</span><span class="sxs-lookup"><span data-stu-id="efd13-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="efd13-116">GRPC 服务实现的构造函数中解析存储共享的状态。</span><span class="sxs-lookup"><span data-stu-id="efd13-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="efd13-117">服务生存期的详细信息，请参阅<xref:fundamentals/dependency-injection#service-lifetimes>。</span><span class="sxs-lookup"><span data-stu-id="efd13-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="efd13-118">添加单独的服务</span><span class="sxs-lookup"><span data-stu-id="efd13-118">Add a singleton service</span></span>

<span data-ttu-id="efd13-119">为了便于从 gRPC C core 实现到 ASP.NET Core 的转换，则可以更改服务实现的服务生存期作用域为单一实例。</span><span class="sxs-lookup"><span data-stu-id="efd13-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="efd13-120">这涉及到将服务实现的实例添加到 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="efd13-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="efd13-121">但是，单一实例生存期的服务实现将不再能够解析通过构造函数注入的作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="efd13-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="efd13-122">配置 gRPC 服务选项</span><span class="sxs-lookup"><span data-stu-id="efd13-122">Configure gRPC services options</span></span>

<span data-ttu-id="efd13-123">在基于 C 的核心应用中，设置，例如`grpc.max_receive_message_length`并`grpc.max_send_message_length`配置了`ChannelOption`时[构造的服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)。</span><span class="sxs-lookup"><span data-stu-id="efd13-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="efd13-124">在 ASP.NET Core 中，`GrpcServiceOptions`使您能够配置这些设置。</span><span class="sxs-lookup"><span data-stu-id="efd13-124">In ASP.NET Core, `GrpcServiceOptions` provides a way to configure these settings.</span></span> <span data-ttu-id="efd13-125">对所有 gRPC 服务或单个服务实现类型，可以全局应用的设置。</span><span class="sxs-lookup"><span data-stu-id="efd13-125">The settings can be applied globally to all gRPC services or to an individual service implementation type.</span></span> <span data-ttu-id="efd13-126">为单个服务实现类型指定的选项重写时配置的全局设置。</span><span class="sxs-lookup"><span data-stu-id="efd13-126">Options specified for individual service implementation types override global settings when configured.</span></span>

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

## <a name="logging"></a><span data-ttu-id="efd13-127">日志记录</span><span class="sxs-lookup"><span data-stu-id="efd13-127">Logging</span></span>

<span data-ttu-id="efd13-128">基于 C 的核心应用程序依赖`GrpcEnvironment`到[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)出于调试目的。</span><span class="sxs-lookup"><span data-stu-id="efd13-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="efd13-129">ASP.NET Core 堆栈提供此功能通过[日志记录 API](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="efd13-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="efd13-130">例如，一个记录器可以添加到通过构造函数注入 gRPC 服务：</span><span class="sxs-lookup"><span data-stu-id="efd13-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="efd13-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="efd13-131">HTTPS</span></span>

<span data-ttu-id="efd13-132">基于 C 的核心应用程序配置通过 HTTPS [Server.Ports 属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)。</span><span class="sxs-lookup"><span data-stu-id="efd13-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="efd13-133">类似的概念用于在 ASP.NET Core 中配置服务器。</span><span class="sxs-lookup"><span data-stu-id="efd13-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="efd13-134">例如，使用 Kestrel[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)实现此功能。</span><span class="sxs-lookup"><span data-stu-id="efd13-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="efd13-135">拦截程序和中间件</span><span class="sxs-lookup"><span data-stu-id="efd13-135">Interceptors and Middleware</span></span>

<span data-ttu-id="efd13-136">ASP.NET Core[中间件](xref:fundamentals/middleware/index)提供类似功能相比 C core 基于 gRPC 应用中的侦听器。</span><span class="sxs-lookup"><span data-stu-id="efd13-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="efd13-137">中间件和侦听器是从概念上讲相同的因为二者都用于构造处理 gRPC 请求管道。</span><span class="sxs-lookup"><span data-stu-id="efd13-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="efd13-138">它们都可用于在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="efd13-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="efd13-139">但是，ASP.NET Core 中间件运行，在基础 HTTP/2 消息，而拦截器对使用抽象的 gRPC 层[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)。</span><span class="sxs-lookup"><span data-stu-id="efd13-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="efd13-140">其他资源</span><span class="sxs-lookup"><span data-stu-id="efd13-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
