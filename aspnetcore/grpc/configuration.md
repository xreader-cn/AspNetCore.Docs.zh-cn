---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何为 ASP.NET Core 应用配置 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310319"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="2f4aa-103">ASP.NET Core 配置的 gRPC</span><span class="sxs-lookup"><span data-stu-id="2f4aa-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="2f4aa-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="2f4aa-104">Configure services options</span></span>

<span data-ttu-id="2f4aa-105">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="2f4aa-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="2f4aa-106">选项</span><span class="sxs-lookup"><span data-stu-id="2f4aa-106">Option</span></span> | <span data-ttu-id="2f4aa-107">Default Value</span><span class="sxs-lookup"><span data-stu-id="2f4aa-107">Default Value</span></span> | <span data-ttu-id="2f4aa-108">描述</span><span class="sxs-lookup"><span data-stu-id="2f4aa-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="2f4aa-109">可从服务器发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="2f4aa-110">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="2f4aa-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="2f4aa-111">4 MB</span></span> | <span data-ttu-id="2f4aa-112">服务器可接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="2f4aa-113">如果服务器收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="2f4aa-114">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f4aa-115">如果`true`为，则在服务方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="2f4aa-116">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-116">The default is `false`.</span></span> <span data-ttu-id="2f4aa-117">设置`EnableDetailedErrors` 为`true`可以泄露敏感信息。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="2f4aa-118">gzip、deflate</span><span class="sxs-lookup"><span data-stu-id="2f4aa-118">gzip, deflate</span></span> | <span data-ttu-id="2f4aa-119">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="2f4aa-120">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="2f4aa-121">默认配置的提供程序支持**gzip**和**deflate**压缩。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-121">The default configured providers support **gzip** and **deflate** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="2f4aa-122">压缩算法用于压缩从服务器发送的消息。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="2f4aa-123">该算法必须与中`CompressionProviders`的压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="2f4aa-124">为了使算法压缩响应，客户端必须通过在**grpc-accept**标头中发送来指示它支持该算法。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="2f4aa-125">用于压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="2f4aa-126">可以通过在中`AddGrpc` `Startup.ConfigureServices`提供调用的选项委托，为所有服务配置选项：</span><span class="sxs-lookup"><span data-stu-id="2f4aa-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="2f4aa-127">单个服务的选项可替代和中`AddGrpc`提供的全局选项，可以使用`AddServiceOptions<TService>`以下方法配置：</span><span class="sxs-lookup"><span data-stu-id="2f4aa-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="2f4aa-128">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="2f4aa-128">Configure client options</span></span>

<span data-ttu-id="2f4aa-129">下表描述了用于配置 gRPC 通道的选项：</span><span class="sxs-lookup"><span data-stu-id="2f4aa-129">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="2f4aa-130">选项</span><span class="sxs-lookup"><span data-stu-id="2f4aa-130">Option</span></span> | <span data-ttu-id="2f4aa-131">Default Value</span><span class="sxs-lookup"><span data-stu-id="2f4aa-131">Default Value</span></span> | <span data-ttu-id="2f4aa-132">描述</span><span class="sxs-lookup"><span data-stu-id="2f4aa-132">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="2f4aa-133">新实例</span><span class="sxs-lookup"><span data-stu-id="2f4aa-133">New instance</span></span> | <span data-ttu-id="2f4aa-134">用于`HttpClient`进行 gRPC 调用的。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-134">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="2f4aa-135">可以将客户端设置为配置自定义`HttpClientHandler`，也可以将其他处理程序添加到 HTTP 管道进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-135">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="2f4aa-136">默认情况下， `HttpClient`会创建一个新实例。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-136">By default a new `HttpClient` instance is created.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="2f4aa-137">可从客户端发送的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-137">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="2f4aa-138">尝试发送超过配置的最大消息大小的消息将导致异常。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-138">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="2f4aa-139">4 MB</span><span class="sxs-lookup"><span data-stu-id="2f4aa-139">4 MB</span></span> | <span data-ttu-id="2f4aa-140">客户端可以接收的最大消息大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-140">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="2f4aa-141">如果服务器收到的消息超过此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-141">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="2f4aa-142">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-142">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `TransportOptions` | `null` | <span data-ttu-id="2f4aa-143">传输选项配置信道如何调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-143">Transport options configure how the channel calls the gRPC service.</span></span> <span data-ttu-id="2f4aa-144">目前，唯一的实现`HttpClientTransport`是选项，用于指定`HttpClient` gRPC 使用的。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-144">Currently the only implementation is `HttpClientTransport` options lets you specify the `HttpClient` used by gRPC.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="2f4aa-145">一个 `ChannelCredentials` 实例。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-145">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="2f4aa-146">凭据用于将身份验证元数据添加到 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-146">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="2f4aa-147">gzip、deflate</span><span class="sxs-lookup"><span data-stu-id="2f4aa-147">gzip, deflate</span></span> | <span data-ttu-id="2f4aa-148">用于压缩和解压缩消息的压缩提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-148">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="2f4aa-149">可以创建自定义压缩提供程序并将其添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-149">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="2f4aa-150">默认配置的提供程序支持**gzip**和**deflate**压缩。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-150">The default configured providers support **gzip** and **deflate** compression.</span></span> |

<span data-ttu-id="2f4aa-151">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="2f4aa-151">The following code:</span></span>

* <span data-ttu-id="2f4aa-152">设置通道上的最大发送和接收消息大小。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-152">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="2f4aa-153">创建客户端。</span><span class="sxs-lookup"><span data-stu-id="2f4aa-153">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="2f4aa-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="2f4aa-154">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
