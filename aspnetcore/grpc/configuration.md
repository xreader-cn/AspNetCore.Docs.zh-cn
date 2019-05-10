---
title: ASP.NET Core 配置的 gRPC
author: jamesnk
description: 了解如何配置适用于 ASP.NET Core 应用 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087354"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="04fbc-103">ASP.NET Core 配置的 gRPC</span><span class="sxs-lookup"><span data-stu-id="04fbc-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="04fbc-104">配置服务选项</span><span class="sxs-lookup"><span data-stu-id="04fbc-104">Configure services options</span></span>

<span data-ttu-id="04fbc-105">下表描述了用于配置 gRPC 服务的选项：</span><span class="sxs-lookup"><span data-stu-id="04fbc-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="04fbc-106">选项</span><span class="sxs-lookup"><span data-stu-id="04fbc-106">Option</span></span> | <span data-ttu-id="04fbc-107">默认值</span><span class="sxs-lookup"><span data-stu-id="04fbc-107">Default Value</span></span> | <span data-ttu-id="04fbc-108">描述</span><span class="sxs-lookup"><span data-stu-id="04fbc-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="04fbc-109">可以从服务器发送的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="04fbc-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="04fbc-110">尝试发送一条消息，超出了配置的最大消息大小会引发异常。</span><span class="sxs-lookup"><span data-stu-id="04fbc-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="04fbc-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="04fbc-111">4 MB</span></span> | <span data-ttu-id="04fbc-112">服务器可以接收的字节数最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="04fbc-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="04fbc-113">如果服务器收到消息超出此限制时，它将引发异常。</span><span class="sxs-lookup"><span data-stu-id="04fbc-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="04fbc-114">增加此值允许服务器以接收更大的消息，但会降低内存占用情况。</span><span class="sxs-lookup"><span data-stu-id="04fbc-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="04fbc-115">如果`true`、 详细的异常消息返回到客户端，在服务方法中引发异常时。</span><span class="sxs-lookup"><span data-stu-id="04fbc-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="04fbc-116">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="04fbc-116">The default is `false`.</span></span> <span data-ttu-id="04fbc-117">此值设置为`true`可能会泄漏敏感信息。</span><span class="sxs-lookup"><span data-stu-id="04fbc-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="04fbc-118">gzip</span><span class="sxs-lookup"><span data-stu-id="04fbc-118">gzip</span></span> | <span data-ttu-id="04fbc-119">压缩来压缩和解压缩消息的提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="04fbc-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="04fbc-120">可以创建自定义压缩提供程序，并将其添加到集合。</span><span class="sxs-lookup"><span data-stu-id="04fbc-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="04fbc-121">配置提供程序支持的默认值**gzip**压缩。</span><span class="sxs-lookup"><span data-stu-id="04fbc-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="04fbc-122">使用可压缩从服务器发送的消息的压缩算法。</span><span class="sxs-lookup"><span data-stu-id="04fbc-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="04fbc-123">该算法必须与匹配中的压缩提供程序`CompressionProviders`。</span><span class="sxs-lookup"><span data-stu-id="04fbc-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="04fbc-124">对于 algorthm 压缩响应客户端必须指示它通过发送支持该算法**grpc 接受编码**标头。</span><span class="sxs-lookup"><span data-stu-id="04fbc-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="04fbc-125">使用可压缩从服务器发送的消息的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="04fbc-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="04fbc-126">可以通过提供一个选项委托到的所有服务配置选项`AddGrpc`调用中`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="04fbc-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="04fbc-127">单个服务的选项重写中提供的全局选项`AddGrpc`，可以使用配置`AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="04fbc-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a><span data-ttu-id="04fbc-128">配置 Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="04fbc-128">Configure Kestrel options</span></span>

<span data-ttu-id="04fbc-129">Kestrel 服务器有影响 for ASP.NET gRPC 的行为的配置选项。</span><span class="sxs-lookup"><span data-stu-id="04fbc-129">Kestrel server has configuration options that affect the behavior of gRPC for ASP.NET.</span></span>

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="04fbc-130">请求正文数据速率限制</span><span class="sxs-lookup"><span data-stu-id="04fbc-130">Request body data rate limit</span></span>

<span data-ttu-id="04fbc-131">默认情况下，Kestrel 服务器施加[最小请求正文数据速率](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>)。</span><span class="sxs-lookup"><span data-stu-id="04fbc-131">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="04fbc-132">有关流式处理客户端和双工流式处理的调用，可能不满足此速率和连接可能超时。最小值请求的正文 gRPC 服务包含流式处理客户端和双工流式处理调用时，必须禁用数据速率限制：</span><span class="sxs-lookup"><span data-stu-id="04fbc-132">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="04fbc-133">其他资源</span><span class="sxs-lookup"><span data-stu-id="04fbc-133">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
