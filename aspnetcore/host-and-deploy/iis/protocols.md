---
title: 在 IIS 中将 ASP.NET Core 和 HTTP/2 结合使用
author: rick-anderson
description: 了解如何将 HTTP/2 功能和 IIS 结合使用。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654337"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="668b9-103">在 IIS 中将 ASP.NET Core 和 HTTP/2 结合使用</span><span class="sxs-lookup"><span data-stu-id="668b9-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="668b9-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="668b9-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="668b9-105">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="668b9-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="668b9-106">Windows Server 2016 或更高版本/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="668b9-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="668b9-107">IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="668b9-107">IIS 10 or later</span></span>
* <span data-ttu-id="668b9-108">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="668b9-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="668b9-109">[进程外托管](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时：面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="668b9-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="668b9-110">对于建立了 HTTP/2 连接时的进程内部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="668b9-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="668b9-111">对于建立了 HTTP/2 连接时的进程外部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="668b9-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="668b9-112">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="668b9-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="668b9-113">默认情况下已为 HTTPS/TLS 连接启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="668b9-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="668b9-114">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="668b9-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="668b9-115">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="668b9-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="668b9-116">用于支持 gRPC 的高级 HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="668b9-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="668b9-117">IIS 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。</span><span class="sxs-lookup"><span data-stu-id="668b9-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="668b9-118">在 IIS 上运行 gRPC 的要求：</span><span class="sxs-lookup"><span data-stu-id="668b9-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="668b9-119">进程内托管。</span><span class="sxs-lookup"><span data-stu-id="668b9-119">In-process hosting.</span></span>
* <span data-ttu-id="668b9-120">Windows 10，OS 内部版本 20300.1000 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="668b9-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="668b9-121">可能需要使用 Windows 预览体验计划内部版本。</span><span class="sxs-lookup"><span data-stu-id="668b9-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="668b9-122">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="668b9-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="668b9-123">预告片</span><span class="sxs-lookup"><span data-stu-id="668b9-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="668b9-124">重置</span><span class="sxs-lookup"><span data-stu-id="668b9-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
