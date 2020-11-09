---
title: 适用于 ASP.NET Core 的 gRPC 的安全注意事项
author: jamesnk
description: 了解适用于 ASP.NET Core 的 gRPC 的安全注意事项。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058826"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="fdc02-103">适用于 ASP.NET Core 的 gRPC 的安全注意事项</span><span class="sxs-lookup"><span data-stu-id="fdc02-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="fdc02-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fdc02-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fdc02-105">本文提供了有关通过 .NET Core 保护 gRPC 的信息。</span><span class="sxs-lookup"><span data-stu-id="fdc02-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="fdc02-106">传输安全性</span><span class="sxs-lookup"><span data-stu-id="fdc02-106">Transport security</span></span>

<span data-ttu-id="fdc02-107">gRPC 消息使用 HTTP/2 进行发送和接收。</span><span class="sxs-lookup"><span data-stu-id="fdc02-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="fdc02-108">我们建议：</span><span class="sxs-lookup"><span data-stu-id="fdc02-108">We recommend:</span></span>

* <span data-ttu-id="fdc02-109">使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 以保护生产 gRPC 应用中的消息。</span><span class="sxs-lookup"><span data-stu-id="fdc02-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="fdc02-110">gRPC 服务应仅侦听并响应受保护的端口。</span><span class="sxs-lookup"><span data-stu-id="fdc02-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="fdc02-111">TLS 是在 Kestrel 中配置的。</span><span class="sxs-lookup"><span data-stu-id="fdc02-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="fdc02-112">有关配置 Kestrel 终结点的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="fdc02-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="fdc02-113">异常</span><span class="sxs-lookup"><span data-stu-id="fdc02-113">Exceptions</span></span>

<span data-ttu-id="fdc02-114">异常消息通常被认为是不应透露给客户端的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="fdc02-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="fdc02-115">默认情况下，gRPC 不会将 gRPC 服务引发的异常的详细信息发送给客户端。</span><span class="sxs-lookup"><span data-stu-id="fdc02-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="fdc02-116">客户端将收到指示出现错误的一般消息。</span><span class="sxs-lookup"><span data-stu-id="fdc02-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="fdc02-117">使用 [EnableDetailedErrors](xref:grpc/configuration#configure-services-options) 可以替代（例如，在开发或测试中）向客户端的异常消息发送。</span><span class="sxs-lookup"><span data-stu-id="fdc02-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="fdc02-118">异常消息不应在生产应用中向客户端公开。</span><span class="sxs-lookup"><span data-stu-id="fdc02-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="fdc02-119">消息大小限制</span><span class="sxs-lookup"><span data-stu-id="fdc02-119">Message size limits</span></span>

<span data-ttu-id="fdc02-120">到 gRPC 客户端和服务的传入消息将加载到内存中。</span><span class="sxs-lookup"><span data-stu-id="fdc02-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="fdc02-121">消息大小限制是一种有助于防止 gRPC 消耗过多资源的机制。</span><span class="sxs-lookup"><span data-stu-id="fdc02-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="fdc02-122">gRPC 使用每个消息的大小限制来管理传入和传出消息。</span><span class="sxs-lookup"><span data-stu-id="fdc02-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="fdc02-123">默认情况下，gRPC 将传入消息限制为 4 MB。</span><span class="sxs-lookup"><span data-stu-id="fdc02-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="fdc02-124">传出消息没有限制。</span><span class="sxs-lookup"><span data-stu-id="fdc02-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="fdc02-125">在服务器上，可以使用 `AddGrpc` 为应用中的所有服务配置 gRPC 消息限制：</span><span class="sxs-lookup"><span data-stu-id="fdc02-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="fdc02-126">还可以使用 `AddServiceOptions<TService>` 为单个服务配置限制。</span><span class="sxs-lookup"><span data-stu-id="fdc02-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="fdc02-127">有关配置消息大小限制的详细信息，请参阅 [gRPC 配置](xref:grpc/configuration)。</span><span class="sxs-lookup"><span data-stu-id="fdc02-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="fdc02-128">客户端证书验证</span><span class="sxs-lookup"><span data-stu-id="fdc02-128">Client certificate validation</span></span>

<span data-ttu-id="fdc02-129">建立连接后，将首先验证[客户端证书](https://tools.ietf.org/html/rfc5246#section-7.4.4)。</span><span class="sxs-lookup"><span data-stu-id="fdc02-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="fdc02-130">默认情况下，Kestrel 不会对连接的客户端证书执行额外验证。</span><span class="sxs-lookup"><span data-stu-id="fdc02-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="fdc02-131">建议由客户端证书保护的 gRPC 服务使用 [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) 包。</span><span class="sxs-lookup"><span data-stu-id="fdc02-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="fdc02-132">ASP.NET Core 认证身份验证将对客户端证书执行其他验证，包括：</span><span class="sxs-lookup"><span data-stu-id="fdc02-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="fdc02-133">验证证书是否具有有效的增强型密钥使用 (EKU)</span><span class="sxs-lookup"><span data-stu-id="fdc02-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="fdc02-134">验证是否在其有效期内</span><span class="sxs-lookup"><span data-stu-id="fdc02-134">Is within its validity period</span></span>
* <span data-ttu-id="fdc02-135">检查证书吊销</span><span class="sxs-lookup"><span data-stu-id="fdc02-135">Check certificate revocation</span></span>
