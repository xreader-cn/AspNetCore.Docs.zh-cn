---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041884"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="2bf60-103">ASP.NET Core 配置的 gRPC</span><span class="sxs-lookup"><span data-stu-id="2bf60-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="2bf60-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="2bf60-104">Configure services options</span></span>

<span data-ttu-id="2bf60-105">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="2bf60-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="2bf60-106">选项</span><span class="sxs-lookup"><span data-stu-id="2bf60-106">Option</span></span> | <span data-ttu-id="2bf60-107">默认值</span><span class="sxs-lookup"><span data-stu-id="2bf60-107">Default Value</span></span> | <span data-ttu-id="2bf60-108">描述</span><span class="sxs-lookup"><span data-stu-id="2bf60-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="2bf60-109">可以从服务器发送的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="2bf60-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="2bf60-110">尝试发送一条消息，超出了配置的最大消息大小会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2bf60-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="2bf60-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="2bf60-111">4 MB</span></span> | <span data-ttu-id="2bf60-112">服务器可以接收的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="2bf60-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="2bf60-113">如果服务器收到消息超出此限制时，它将引发异常。</span><span class="sxs-lookup"><span data-stu-id="2bf60-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="2bf60-114">增加此值允许服务器以接收更大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="2bf60-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2bf60-115">如果`true`、 详细的异常消息返回到客户端，在服务方法中引发异常时。</span><span class="sxs-lookup"><span data-stu-id="2bf60-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="2bf60-116">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2bf60-116">The default is `false`.</span></span> <span data-ttu-id="2bf60-117">此值设置为`true`可能会泄漏敏感信息。</span><span class="sxs-lookup"><span data-stu-id="2bf60-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="2bf60-118">gzip</span><span class="sxs-lookup"><span data-stu-id="2bf60-118">gzip</span></span> | <span data-ttu-id="2bf60-119">压缩来压缩和解压缩消息的提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="2bf60-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="2bf60-120">可以创建自定义压缩提供程序，并将其添加到集合。</span><span class="sxs-lookup"><span data-stu-id="2bf60-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="2bf60-121">配置提供程序支持的默认值**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="2bf60-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="2bf60-122">使用可压缩从服务器发送的消息的压缩算法。</span><span class="sxs-lookup"><span data-stu-id="2bf60-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="2bf60-123">该算法必须与匹配中的压缩提供程序`CompressionProviders`。</span><span class="sxs-lookup"><span data-stu-id="2bf60-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="2bf60-124">对于 algorthm 压缩响应客户端必须指示它通过发送支持该算法**grpc 接受编码**标头。</span><span class="sxs-lookup"><span data-stu-id="2bf60-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="2bf60-125">使用可压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="2bf60-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="2bf60-126">可以通过提供一个选项委托到的所有服务配置选项`AddGrpc`调用中`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="2bf60-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="2bf60-127">单个服务的选项重写中提供的全局选项`AddGrpc`，可以使用配置`AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="2bf60-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a><span data-ttu-id="2bf60-128">其他资源</span><span class="sxs-lookup"><span data-stu-id="2bf60-128">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
