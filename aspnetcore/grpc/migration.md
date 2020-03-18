---
title: 将 gRPC 服务从 C-core 迁移到 ASP.NET Core
author: juntaoluo
description: 了解如何移动现有的基于 C-core 的 gRPC 应用，以在 ASP.NET Core 堆栈之上运行。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649368"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="fc726-103">将 gRPC 服务从 C-core 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc726-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="fc726-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="fc726-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="fc726-105">由于基础堆栈的实现，不是所有功能在[基于 C-core 的 gRPC](https://grpc.io/blog/grpc-stacks) 应用和基于 ASP.NET Core 的应用之间都以相同的方式工作。</span><span class="sxs-lookup"><span data-stu-id="fc726-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="fc726-106">本文档重点介绍在两个堆栈之间迁移的主要区别。</span><span class="sxs-lookup"><span data-stu-id="fc726-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="fc726-107">gRPC 服务实现生存期</span><span class="sxs-lookup"><span data-stu-id="fc726-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="fc726-108">在 ASP.NET Core 堆栈中，默认情况下，创建的 gRPC 服务具有[范围内的生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="fc726-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="fc726-109">与此相反，默认情况下，gRPC C-core 会绑定到具有[单一实例生存期](xref:fundamentals/dependency-injection#service-lifetimes)的服务。</span><span class="sxs-lookup"><span data-stu-id="fc726-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="fc726-110">范围内的生存期允许服务实现解析具有范围内的生存期的其他服务。</span><span class="sxs-lookup"><span data-stu-id="fc726-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="fc726-111">例如，范围内的生存期还可以通过构造函数注入解析 DI 容器中的 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="fc726-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="fc726-112">使用范围内的生存期：</span><span class="sxs-lookup"><span data-stu-id="fc726-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="fc726-113">为每个请求构造服务实现的新实例。</span><span class="sxs-lookup"><span data-stu-id="fc726-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="fc726-114">不能通过实现类型上的实例成员在请求之间共享状态。</span><span class="sxs-lookup"><span data-stu-id="fc726-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="fc726-115">预期是在 DI 容器的单一实例服务中存储共享状态。</span><span class="sxs-lookup"><span data-stu-id="fc726-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="fc726-116">在 gRPC 服务实现的构造函数中解析存储的共享状态。</span><span class="sxs-lookup"><span data-stu-id="fc726-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="fc726-117">有关服务生存期的详细信息，请参阅 <xref:fundamentals/dependency-injection#service-lifetimes>。</span><span class="sxs-lookup"><span data-stu-id="fc726-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="fc726-118">添加单一实例服务</span><span class="sxs-lookup"><span data-stu-id="fc726-118">Add a singleton service</span></span>

<span data-ttu-id="fc726-119">为了促进从 gRPC C-core 实现到 ASP.NET Core 的转换，可以将服务实现的服务生存期从范围内改为单一实例。</span><span class="sxs-lookup"><span data-stu-id="fc726-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="fc726-120">这涉及到将服务实现的实例添加到 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="fc726-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="fc726-121">但是，具有单一实例生存期的服务实现不再能够通过构造函数注入来解析范围内的服务。</span><span class="sxs-lookup"><span data-stu-id="fc726-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="fc726-122">配置 gRPC 服务选项</span><span class="sxs-lookup"><span data-stu-id="fc726-122">Configure gRPC services options</span></span>

<span data-ttu-id="fc726-123">在基于 C-core 的应用中，[构造服务器实例](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)时，使用 `ChannelOption` 配置 `grpc.max_receive_message_length` 和 `grpc.max_send_message_length` 等设置。</span><span class="sxs-lookup"><span data-stu-id="fc726-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="fc726-124">在 ASP.NET Core 中，gRPC 通过 `GrpcServiceOptions` 类型提供配置。</span><span class="sxs-lookup"><span data-stu-id="fc726-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="fc726-125">例如，可以通过 `AddGrpc` 配置 gRPC 服务的最大传入消息大小。</span><span class="sxs-lookup"><span data-stu-id="fc726-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="fc726-126">下面的示例将默认 `MaxReceiveMessageSize` 4 MB 更改为 16 MB：</span><span class="sxs-lookup"><span data-stu-id="fc726-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="fc726-127">有关配置的详细信息，请参阅 <xref:grpc/configuration>。</span><span class="sxs-lookup"><span data-stu-id="fc726-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="fc726-128">Logging</span><span class="sxs-lookup"><span data-stu-id="fc726-128">Logging</span></span>

<span data-ttu-id="fc726-129">基于 C-core 的应用依赖于 `GrpcEnvironment` 来[配置记录器](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)以进行调试。</span><span class="sxs-lookup"><span data-stu-id="fc726-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="fc726-130">ASP.NET Core 堆栈通过 [Logging API](xref:fundamentals/logging/index) 提供此功能。</span><span class="sxs-lookup"><span data-stu-id="fc726-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="fc726-131">例如，可以通过构造函数注入将记录器添加到 gRPC 服务：</span><span class="sxs-lookup"><span data-stu-id="fc726-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="fc726-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="fc726-132">HTTPS</span></span>

<span data-ttu-id="fc726-133">基于 C-core 的应用通过 [ 属性](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="fc726-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="fc726-134">类似的概念用于在 ASP.NET Core 中配置服务器。</span><span class="sxs-lookup"><span data-stu-id="fc726-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="fc726-135">例如，Kestrel 将[终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)用于此目的。</span><span class="sxs-lookup"><span data-stu-id="fc726-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="fc726-136">gRPC 拦截器与中间件</span><span class="sxs-lookup"><span data-stu-id="fc726-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="fc726-137">与基于 C-core 的 gRPC 应用中的拦截器相比，ASP.NET Core [中间件](xref:fundamentals/middleware/index)提供类似功能。</span><span class="sxs-lookup"><span data-stu-id="fc726-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="fc726-138">ASP.NET Core 中间件和拦截器在概念上类似。</span><span class="sxs-lookup"><span data-stu-id="fc726-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="fc726-139">两者：</span><span class="sxs-lookup"><span data-stu-id="fc726-139">Both:</span></span>

* <span data-ttu-id="fc726-140">用于构造处理 gRPC 请求的管道。</span><span class="sxs-lookup"><span data-stu-id="fc726-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="fc726-141">允许在管道中的下一个组件前或后执行工作。</span><span class="sxs-lookup"><span data-stu-id="fc726-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="fc726-142">提供对 `HttpContext` 的访问权限：</span><span class="sxs-lookup"><span data-stu-id="fc726-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="fc726-143">在中间件中，`HttpContext` 是参数。</span><span class="sxs-lookup"><span data-stu-id="fc726-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="fc726-144">在拦截器中，可以通过 `ServerCallContext.GetHttpContext` 扩展方法使用 `ServerCallContext` 参数访问 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="fc726-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="fc726-145">请注意，此功能特定于在 ASP.NET Core 中运行的拦截器。</span><span class="sxs-lookup"><span data-stu-id="fc726-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="fc726-146">gRPC 拦截器与 ASP.NET Core 中间件的不同之处：</span><span class="sxs-lookup"><span data-stu-id="fc726-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="fc726-147">拦截器：</span><span class="sxs-lookup"><span data-stu-id="fc726-147">Interceptors:</span></span>
  * <span data-ttu-id="fc726-148">使用 [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html) 在 gRPC 抽象层上操作。</span><span class="sxs-lookup"><span data-stu-id="fc726-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="fc726-149">提供对以下内容的访问权限：</span><span class="sxs-lookup"><span data-stu-id="fc726-149">Provide access to:</span></span>
    * <span data-ttu-id="fc726-150">发送到调用的反序列化消息。</span><span class="sxs-lookup"><span data-stu-id="fc726-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="fc726-151">序列化之前从调用返回的消息。</span><span class="sxs-lookup"><span data-stu-id="fc726-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="fc726-152">可以捕获和处理从 gRPC 服务引发的异常。</span><span class="sxs-lookup"><span data-stu-id="fc726-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="fc726-153">中间件：</span><span class="sxs-lookup"><span data-stu-id="fc726-153">Middleware:</span></span>
  * <span data-ttu-id="fc726-154">在 gRPC 拦截器之前运行。</span><span class="sxs-lookup"><span data-stu-id="fc726-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="fc726-155">对基础 HTTP/2 消息进行操作。</span><span class="sxs-lookup"><span data-stu-id="fc726-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="fc726-156">只能访问请求和响应流中的字节。</span><span class="sxs-lookup"><span data-stu-id="fc726-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc726-157">其他资源</span><span class="sxs-lookup"><span data-stu-id="fc726-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
