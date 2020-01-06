---
title: gRPC for .NET 配置
author: jamesnk
description: 了解如何配置适用于 .NET 应用的 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355734"
---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="4f1aa-103">gRPC for .NET 配置</span><span class="sxs-lookup"><span data-stu-id="4f1aa-103">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="4f1aa-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="4f1aa-104">Configure services options</span></span>

<span data-ttu-id="4f1aa-105">在*Startup.cs*中 `AddGrpc` 配置 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-105">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="4f1aa-106">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="4f1aa-106">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="4f1aa-107">选项</span><span class="sxs-lookup"><span data-stu-id="4f1aa-107">Option</span></span> | <span data-ttu-id="4f1aa-108">默认值</span><span class="sxs-lookup"><span data-stu-id="4f1aa-108">Default Value</span></span> | <span data-ttu-id="4f1aa-109">描述</span><span class="sxs-lookup"><span data-stu-id="4f1aa-109">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="4f1aa-110">可从服务器发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-110">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="4f1aa-111">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-111">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="4f1aa-112">4 MB</span><span class="sxs-lookup"><span data-stu-id="4f1aa-112">4 MB</span></span> | <span data-ttu-id="4f1aa-113">服务器可接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-113">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="4f1aa-114">如果服务器收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-114">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="4f1aa-115">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-115">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4f1aa-116">如果 `true`，则当服务方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-116">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="4f1aa-117">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-117">The default is `false`.</span></span> <span data-ttu-id="4f1aa-118">将 `EnableDetailedErrors` 设置为 `true` 可能会泄漏敏感信息。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-118">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="4f1aa-119">gzip</span><span class="sxs-lookup"><span data-stu-id="4f1aa-119">gzip</span></span> | <span data-ttu-id="4f1aa-120">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-120">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="4f1aa-121">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-121">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="4f1aa-122">默认配置的提供程序支持**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-122">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="4f1aa-123">压缩算法用于压缩从服务器发送的消息。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-123">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="4f1aa-124">该算法必须与 `CompressionProviders`中的压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-124">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="4f1aa-125">为了使算法压缩响应，客户端必须通过在**grpc-accept**标头中发送来指示它支持该算法。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-125">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="4f1aa-126">用于压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-126">The compress level used to compress messages sent from the server.</span></span> |
| `Interceptors` | <span data-ttu-id="4f1aa-127">无</span><span class="sxs-lookup"><span data-stu-id="4f1aa-127">None</span></span> | <span data-ttu-id="4f1aa-128">运行每个 gRPC 调用的侦听器的集合。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-128">A collection of interceptors that are run with each gRPC call.</span></span> <span data-ttu-id="4f1aa-129">拦截按注册顺序运行。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-129">Interceptors are run in the order they are registered.</span></span> <span data-ttu-id="4f1aa-130">全局配置的侦听器在为单个服务配置侦听器之前运行。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-130">Globally configured interceptors are run before interceptors configured for a single service.</span></span> <span data-ttu-id="4f1aa-131">有关 gRPC 侦听器的详细信息，请参阅[GRPC 侦听器和中间件](xref:grpc/migration#grpc-interceptors-vs-middleware)。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-131">For more information about gRPC interceptors, see [gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware).</span></span> |

<span data-ttu-id="4f1aa-132">可以通过向 `Startup.ConfigureServices`中的 `AddGrpc` 调用提供选项委托，为所有服务配置选项：</span><span class="sxs-lookup"><span data-stu-id="4f1aa-132">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="4f1aa-133">单个服务的选项将覆盖 `AddGrpc` 中提供的全局选项，并且可以使用 `AddServiceOptions<TService>`进行配置：</span><span class="sxs-lookup"><span data-stu-id="4f1aa-133">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="4f1aa-134">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="4f1aa-134">Configure client options</span></span>

<span data-ttu-id="4f1aa-135">在 `GrpcChannelOptions`上设置 gRPC 客户端配置。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-135">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="4f1aa-136">下表描述了用于配置 gRPC 通道的选项：</span><span class="sxs-lookup"><span data-stu-id="4f1aa-136">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="4f1aa-137">选项</span><span class="sxs-lookup"><span data-stu-id="4f1aa-137">Option</span></span> | <span data-ttu-id="4f1aa-138">默认值</span><span class="sxs-lookup"><span data-stu-id="4f1aa-138">Default Value</span></span> | <span data-ttu-id="4f1aa-139">描述</span><span class="sxs-lookup"><span data-stu-id="4f1aa-139">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="4f1aa-140">新实例</span><span class="sxs-lookup"><span data-stu-id="4f1aa-140">New instance</span></span> | <span data-ttu-id="4f1aa-141">用于进行 gRPC 调用的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-141">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="4f1aa-142">可以将客户端设置为配置自定义 `HttpClientHandler`，或将其他处理程序添加到 gRPC 调用的 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-142">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="4f1aa-143">如果未指定 `HttpClient`，则将为该通道创建新的 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-143">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="4f1aa-144">它将自动被释放。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-144">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="4f1aa-145">如果 `true`并且指定了 `HttpClient`，则在释放 `GrpcChannel` 时将释放 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-145">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="4f1aa-146">客户端用来记录有关 gRPC 调用的信息的 `LoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-146">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="4f1aa-147">可以通过依赖关系注入或使用 `LoggerFactory.Create`创建 `LoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-147">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="4f1aa-148">有关配置日志记录的示例，请参阅 <xref:grpc/diagnostics#grpc-client-logging>。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-148">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="4f1aa-149">可从客户端发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-149">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="4f1aa-150">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-150">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="4f1aa-151">4 MB</span><span class="sxs-lookup"><span data-stu-id="4f1aa-151">4 MB</span></span> | <span data-ttu-id="4f1aa-152">客户端可以接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-152">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="4f1aa-153">如果客户端收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-153">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="4f1aa-154">增大此值可使客户端接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-154">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="4f1aa-155">一个 `ChannelCredentials` 实例。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-155">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="4f1aa-156">凭据用于将身份验证元数据添加到 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-156">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="4f1aa-157">gzip</span><span class="sxs-lookup"><span data-stu-id="4f1aa-157">gzip</span></span> | <span data-ttu-id="4f1aa-158">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-158">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="4f1aa-159">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-159">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="4f1aa-160">默认配置的提供程序支持**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-160">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="4f1aa-161">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="4f1aa-161">The following code:</span></span>

* <span data-ttu-id="4f1aa-162">设置通道上的最大发送和接收消息大小。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-162">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="4f1aa-163">创建客户端。</span><span class="sxs-lookup"><span data-stu-id="4f1aa-163">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="4f1aa-164">其他资源</span><span class="sxs-lookup"><span data-stu-id="4f1aa-164">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
