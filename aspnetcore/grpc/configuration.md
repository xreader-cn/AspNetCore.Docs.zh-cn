---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/30/2019
uid: grpc/configuration
ms.openlocfilehash: e269d701f45c0b852a9006107f0162cc5af2c38a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814928"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="cb5b3-103">ASP.NET Core 配置的 gRPC</span><span class="sxs-lookup"><span data-stu-id="cb5b3-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="cb5b3-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="cb5b3-104">Configure services options</span></span>

<span data-ttu-id="cb5b3-105">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="cb5b3-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="cb5b3-106">选项</span><span class="sxs-lookup"><span data-stu-id="cb5b3-106">Option</span></span> | <span data-ttu-id="cb5b3-107">默认值</span><span class="sxs-lookup"><span data-stu-id="cb5b3-107">Default Value</span></span> | <span data-ttu-id="cb5b3-108">描述</span><span class="sxs-lookup"><span data-stu-id="cb5b3-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="cb5b3-109">可以从服务器发送的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="cb5b3-110">尝试发送一条消息，超出了配置的最大消息大小会引发异常。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="cb5b3-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="cb5b3-111">4 MB</span></span> | <span data-ttu-id="cb5b3-112">服务器可以接收的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="cb5b3-113">如果服务器收到消息超出此限制时，它将引发异常。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="cb5b3-114">增加此值允许服务器以接收更大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb5b3-115">如果`true`、 详细的异常消息返回到客户端，在服务方法中引发异常时。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="cb5b3-116">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-116">The default is `false`.</span></span> <span data-ttu-id="cb5b3-117">设置`EnableDetailedErrors`到`true`可能会泄漏敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="cb5b3-118">gzip</span><span class="sxs-lookup"><span data-stu-id="cb5b3-118">gzip</span></span> | <span data-ttu-id="cb5b3-119">压缩来压缩和解压缩消息的提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="cb5b3-120">可以创建自定义压缩提供程序，并将其添加到集合。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="cb5b3-121">配置提供程序支持的默认值**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="cb5b3-122">使用可压缩从服务器发送的消息的压缩算法。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="cb5b3-123">该算法必须与匹配中的压缩提供程序`CompressionProviders`。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="cb5b3-124">对于要压缩响应的算法，客户端必须指示它通过发送支持该算法**grpc 接受编码**标头。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="cb5b3-125">使用可压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="cb5b3-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="cb5b3-126">可以通过提供一个选项委托到的所有服务配置选项`AddGrpc`调用中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cb5b3-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="cb5b3-127">单个服务的选项重写中提供的全局选项`AddGrpc`，可以使用配置`AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="cb5b3-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="cb5b3-128">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cb5b3-128">Configure client options</span></span>

<span data-ttu-id="cb5b3-129">下面的代码设置客户端最大发送和接收消息大小：</span><span class="sxs-lookup"><span data-stu-id="cb5b3-129">The following code sets the client maximum send and receive message size:</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a><span data-ttu-id="cb5b3-130">其他资源</span><span class="sxs-lookup"><span data-stu-id="cb5b3-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
