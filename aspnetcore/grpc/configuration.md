---
title: gRPC for .NET 配置
author: jamesnk
description: 了解如何配置适用于 .NET 应用的 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 42574b43b4751efc37ff3a827716df4cb8130842
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71199081"
---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="6583c-103">gRPC for .NET 配置</span><span class="sxs-lookup"><span data-stu-id="6583c-103">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="6583c-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="6583c-104">Configure services options</span></span>

<span data-ttu-id="6583c-105">gRPC 服务是在`AddGrpc` *Startup.cs*中配置的。</span><span class="sxs-lookup"><span data-stu-id="6583c-105">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="6583c-106">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="6583c-106">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="6583c-107">选项</span><span class="sxs-lookup"><span data-stu-id="6583c-107">Option</span></span> | <span data-ttu-id="6583c-108">默认值</span><span class="sxs-lookup"><span data-stu-id="6583c-108">Default Value</span></span> | <span data-ttu-id="6583c-109">描述</span><span class="sxs-lookup"><span data-stu-id="6583c-109">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="6583c-110">可从服务器发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="6583c-110">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="6583c-111">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="6583c-111">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="6583c-112">4 MB</span><span class="sxs-lookup"><span data-stu-id="6583c-112">4 MB</span></span> | <span data-ttu-id="6583c-113">服务器可接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="6583c-113">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="6583c-114">如果服务器收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="6583c-114">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="6583c-115">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="6583c-115">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6583c-116">如果`true`为，则在服务方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="6583c-116">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="6583c-117">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="6583c-117">The default is `false`.</span></span> <span data-ttu-id="6583c-118">设置`EnableDetailedErrors` 为`true`可以泄露敏感信息。</span><span class="sxs-lookup"><span data-stu-id="6583c-118">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="6583c-119">Gzip</span><span class="sxs-lookup"><span data-stu-id="6583c-119">gzip</span></span> | <span data-ttu-id="6583c-120">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="6583c-120">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="6583c-121">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="6583c-121">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="6583c-122">默认配置的提供程序支持**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="6583c-122">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="6583c-123">压缩算法用于压缩从服务器发送的消息。</span><span class="sxs-lookup"><span data-stu-id="6583c-123">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="6583c-124">该算法必须与中`CompressionProviders`的压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="6583c-124">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="6583c-125">为了使算法压缩响应，客户端必须通过在**grpc-accept**标头中发送来指示它支持该算法。</span><span class="sxs-lookup"><span data-stu-id="6583c-125">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="6583c-126">用于压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="6583c-126">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="6583c-127">可以通过在中`AddGrpc` `Startup.ConfigureServices`提供调用的选项委托，为所有服务配置选项：</span><span class="sxs-lookup"><span data-stu-id="6583c-127">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="6583c-128">单个服务的选项可替代和中`AddGrpc`提供的全局选项，可以使用`AddServiceOptions<TService>`以下方法配置：</span><span class="sxs-lookup"><span data-stu-id="6583c-128">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="6583c-129">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="6583c-129">Configure client options</span></span>

<span data-ttu-id="6583c-130">gRPC 客户端配置设置为`GrpcChannelOptions`on。</span><span class="sxs-lookup"><span data-stu-id="6583c-130">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="6583c-131">下表描述了用于配置 gRPC 通道的选项：</span><span class="sxs-lookup"><span data-stu-id="6583c-131">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="6583c-132">选项</span><span class="sxs-lookup"><span data-stu-id="6583c-132">Option</span></span> | <span data-ttu-id="6583c-133">默认值</span><span class="sxs-lookup"><span data-stu-id="6583c-133">Default Value</span></span> | <span data-ttu-id="6583c-134">描述</span><span class="sxs-lookup"><span data-stu-id="6583c-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="6583c-135">新实例</span><span class="sxs-lookup"><span data-stu-id="6583c-135">New instance</span></span> | <span data-ttu-id="6583c-136">用于`HttpClient`进行 gRPC 调用的。</span><span class="sxs-lookup"><span data-stu-id="6583c-136">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="6583c-137">可以将客户端设置为配置自定义`HttpClientHandler`，也可以将其他处理程序添加到 HTTP 管道进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="6583c-137">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="6583c-138">如果未`HttpClient`指定，则为通道创建`HttpClient`新的实例。</span><span class="sxs-lookup"><span data-stu-id="6583c-138">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="6583c-139">它将自动被释放。</span><span class="sxs-lookup"><span data-stu-id="6583c-139">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="6583c-140">如果`true`指定了`HttpClient`和，则在释放时`HttpClient`将释放`GrpcChannel`该实例。</span><span class="sxs-lookup"><span data-stu-id="6583c-140">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="6583c-141">客户`LoggerFactory`端用来记录有关 gRPC 调用的信息的。</span><span class="sxs-lookup"><span data-stu-id="6583c-141">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="6583c-142">可以通过依赖关系注入来解析`LoggerFactory.Create`实例，也可以使用创建实例。`LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="6583c-142">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="6583c-143">有关配置日志记录的示例， <xref:grpc/diagnostics#grpc-client-logging>请参阅。</span><span class="sxs-lookup"><span data-stu-id="6583c-143">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="6583c-144">可从客户端发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="6583c-144">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="6583c-145">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="6583c-145">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="6583c-146">4 MB</span><span class="sxs-lookup"><span data-stu-id="6583c-146">4 MB</span></span> | <span data-ttu-id="6583c-147">客户端可以接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="6583c-147">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="6583c-148">如果客户端收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="6583c-148">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="6583c-149">增大此值可使客户端接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="6583c-149">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="6583c-150">一个 `ChannelCredentials` 实例。</span><span class="sxs-lookup"><span data-stu-id="6583c-150">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="6583c-151">凭据用于将身份验证元数据添加到 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="6583c-151">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="6583c-152">Gzip</span><span class="sxs-lookup"><span data-stu-id="6583c-152">gzip</span></span> | <span data-ttu-id="6583c-153">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="6583c-153">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="6583c-154">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="6583c-154">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="6583c-155">默认配置的提供程序支持**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="6583c-155">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="6583c-156">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="6583c-156">The following code:</span></span>

* <span data-ttu-id="6583c-157">设置通道上的最大发送和接收消息大小。</span><span class="sxs-lookup"><span data-stu-id="6583c-157">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="6583c-158">创建客户端。</span><span class="sxs-lookup"><span data-stu-id="6583c-158">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="6583c-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="6583c-159">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
