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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="d19cf-103">将 gRPC services 从 C 核迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d19cf-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="d19cf-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d19cf-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d19cf-105">由于底层堆栈的实现，并非所有功能在[基于 C 核的 gRPC](https://grpc.io/blog/grpc-stacks)应用与基于 ASP.NET Core 的应用程序之间的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="d19cf-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="d19cf-106">本文档重点介绍两个堆栈之间的迁移的主要区别。</span><span class="sxs-lookup"><span data-stu-id="d19cf-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="d19cf-107">gRPC 服务实现生存期</span><span class="sxs-lookup"><span data-stu-id="d19cf-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="d19cf-108">在 ASP.NET Core 堆栈中，默认情况下，使用范围内的[生存期](xref:fundamentals/dependency-injection#service-lifetimes)创建 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="d19cf-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d19cf-109">与此相反，默认情况下 gRPC C 核心将绑定到[单一实例生存期内](xref:fundamentals/dependency-injection#service-lifetimes)的服务。</span><span class="sxs-lookup"><span data-stu-id="d19cf-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="d19cf-110">范围内的生存期允许服务实现使用范围内的生存期解析其他服务。</span><span class="sxs-lookup"><span data-stu-id="d19cf-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="d19cf-111">例如，作用域内的生存期还可以通过构造函数注入解析 DI 容器 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="d19cf-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="d19cf-112">使用范围生存期：</span><span class="sxs-lookup"><span data-stu-id="d19cf-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="d19cf-113">为每个请求构造服务实现的新实例。</span><span class="sxs-lookup"><span data-stu-id="d19cf-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="d19cf-114">不能通过实现类型上的实例成员来共享请求之间的状态。</span><span class="sxs-lookup"><span data-stu-id="d19cf-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="d19cf-115">预期是在 DI 容器的单一实例服务中存储共享状态。</span><span class="sxs-lookup"><span data-stu-id="d19cf-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="d19cf-116">在 gRPC 服务实现的构造函数中解析存储的共享状态。</span><span class="sxs-lookup"><span data-stu-id="d19cf-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="d19cf-117">有关服务生存期的详细信息，请参阅 <xref:fundamentals/dependency-injection#service-lifetimes>。</span><span class="sxs-lookup"><span data-stu-id="d19cf-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="d19cf-118">添加单一实例服务</span><span class="sxs-lookup"><span data-stu-id="d19cf-118">Add a singleton service</span></span>

<span data-ttu-id="d19cf-119">为了促进从 gRPC 的 C 核心实现到 ASP.NET Core 的转换，可以将服务实现的服务生存期从作用域改为单一实例。</span><span class="sxs-lookup"><span data-stu-id="d19cf-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="d19cf-120">这涉及到将服务实现的实例添加到 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="d19cf-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="d19cf-121">但是，具有单一生存期的服务实现不再能够通过构造函数注入来解析范围内的服务。</span><span class="sxs-lookup"><span data-stu-id="d19cf-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="d19cf-122">配置 gRPC services 选项</span><span class="sxs-lookup"><span data-stu-id="d19cf-122">Configure gRPC services options</span></span>

<span data-ttu-id="d19cf-123">在基于 C 的应用程序中，当[构造服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)时，将在 `ChannelOption` 配置 `grpc.max_receive_message_length` 和 `grpc.max_send_message_length` 等设置。</span><span class="sxs-lookup"><span data-stu-id="d19cf-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="d19cf-124">在 ASP.NET Core 中，gRPC 通过 `GrpcServiceOptions` 类型提供配置。</span><span class="sxs-lookup"><span data-stu-id="d19cf-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="d19cf-125">例如，可以通过 `AddGrpc` 配置 gRPC 服务的最大传入消息大小。</span><span class="sxs-lookup"><span data-stu-id="d19cf-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="d19cf-126">下面的示例将默认 `MaxReceiveMessageSize` 为 4 MB 到 16 MB：</span><span class="sxs-lookup"><span data-stu-id="d19cf-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="d19cf-127">有关配置的详细信息，请参阅 <xref:grpc/configuration>。</span><span class="sxs-lookup"><span data-stu-id="d19cf-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="d19cf-128">Logging</span><span class="sxs-lookup"><span data-stu-id="d19cf-128">Logging</span></span>

<span data-ttu-id="d19cf-129">基于 C 核的应用依赖于 `GrpcEnvironment` 来[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)，以便进行调试。</span><span class="sxs-lookup"><span data-stu-id="d19cf-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="d19cf-130">ASP.NET Core 堆栈通过[日志记录 API](xref:fundamentals/logging/index)提供此功能。</span><span class="sxs-lookup"><span data-stu-id="d19cf-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d19cf-131">例如，可以通过构造函数注入将记录器添加到 gRPC 服务：</span><span class="sxs-lookup"><span data-stu-id="d19cf-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="d19cf-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d19cf-132">HTTPS</span></span>

<span data-ttu-id="d19cf-133">基于 C 核的应用通过[服务器端口属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d19cf-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="d19cf-134">类似的概念用于在 ASP.NET Core 中配置服务器。</span><span class="sxs-lookup"><span data-stu-id="d19cf-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="d19cf-135">例如，Kestrel 使用[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)来实现此功能。</span><span class="sxs-lookup"><span data-stu-id="d19cf-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="d19cf-136">拦截和中间件</span><span class="sxs-lookup"><span data-stu-id="d19cf-136">Interceptors and Middleware</span></span>

<span data-ttu-id="d19cf-137">与基于 gRPC 的应用中的侦听器相比，[中间件](xref:fundamentals/middleware/index)提供的功能类似。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d19cf-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="d19cf-138">中间件和侦听器在概念上是相同的，它们用于构造处理 gRPC 请求的管道。</span><span class="sxs-lookup"><span data-stu-id="d19cf-138">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="d19cf-139">它们都允许在管道中的下一个组件之前或之后执行工作。</span><span class="sxs-lookup"><span data-stu-id="d19cf-139">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="d19cf-140">但 ASP.NET Core 中间件对基础 HTTP/2 消息进行操作，而侦听器则使用[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)在 gRPC 抽象层上操作。</span><span class="sxs-lookup"><span data-stu-id="d19cf-140">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d19cf-141">其他资源</span><span class="sxs-lookup"><span data-stu-id="d19cf-141">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
