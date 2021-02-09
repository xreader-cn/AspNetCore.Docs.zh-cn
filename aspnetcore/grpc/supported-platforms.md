---
title: .NET 上的 gRPC 支持的平台
author: jamesnk
description: 了解 .NET 上的 gRPC 支持的平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
no-loc:
- appsettings.json
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530159"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="0e5d8-103">.NET 上的 gRPC 支持的平台</span><span class="sxs-lookup"><span data-stu-id="0e5d8-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="0e5d8-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0e5d8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0e5d8-105">本文讨论在 .NET 中使用 gRPC 的要求和支持的平台。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="0e5d8-106">gRPC 充分利用 HTTP/2 中提供的高级功能。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="0e5d8-107">在任何位置都不支持 HTTP/2，但使用 HTTP/1.1 的第二种线路格式可用于 gRPC：</span><span class="sxs-lookup"><span data-stu-id="0e5d8-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="0e5d8-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2 上的 gRPC 是 gRPC 的常见使用方式。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="0e5d8-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web 修改 gRPC 协议，使其与 HTTP/1.1 兼容。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="0e5d8-110">gRPC-Web 可用于更多位置，尤其是它可由浏览器应用调用。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="0e5d8-111">不再支持两个高级 gRPC 功能：客户端流式处理和双向流式处理。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="0e5d8-112">.NET 上的 gRPC 支持两种线路格式。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="0e5d8-113">默认情况下，使用 HTTP/2 上的 gRPC。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="0e5d8-114">有关设置 gRPC-Web 的信息，请参阅 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="0e5d8-115">设备要求</span><span class="sxs-lookup"><span data-stu-id="0e5d8-115">Device requirements</span></span>

<span data-ttu-id="0e5d8-116">.NET 上的 gRPC 支持 .NET Core 支持的任何设备。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0e5d8-117">Windows</span><span class="sxs-lookup"><span data-stu-id="0e5d8-117">Windows</span></span>
> * <span data-ttu-id="0e5d8-118">Linux</span><span class="sxs-lookup"><span data-stu-id="0e5d8-118">Linux</span></span>
> * <span data-ttu-id="0e5d8-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="0e5d8-119">macOS&dagger;</span></span>
> * <span data-ttu-id="0e5d8-120">浏览器&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0e5d8-120">Browsers&Dagger;</span></span>

<span data-ttu-id="0e5d8-121">&dagger;[macOS 不支持通过 HTTPS 托管 ASP.NET Core 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="0e5d8-122">macOS 上的 gRPC 客户端可以调用使用 HTTPS 的远程服务。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="0e5d8-123">&Dagger;Blazor WebAssembly 应用可以使用 gRPC-Web 调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="0e5d8-124">ASP.NET Core 服务器要求</span><span class="sxs-lookup"><span data-stu-id="0e5d8-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="0e5d8-125">gRPC 服务可在所有内置 ASP.NET Core 服务器上托管。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0e5d8-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0e5d8-126">Kestrel</span></span>
> * <span data-ttu-id="0e5d8-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="0e5d8-127">TestServer</span></span>
> * <span data-ttu-id="0e5d8-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="0e5d8-128">IIS&dagger;</span></span>
> * <span data-ttu-id="0e5d8-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0e5d8-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="0e5d8-130">&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="0e5d8-131">&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="0e5d8-132">有关配置 ASP.NET Core 服务器以运行 gRPC 的信息，请参阅 <xref:grpc/aspnetcore#server-options>。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="0e5d8-133">.NET 版本要求</span><span class="sxs-lookup"><span data-stu-id="0e5d8-133">.NET version requirements</span></span>

<span data-ttu-id="0e5d8-134">.NET 上的 gRPC 支持 .NET Core 3 和 .NET 5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0e5d8-135">.NET 5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0e5d8-135">.NET 5 or later</span></span>
> * <span data-ttu-id="0e5d8-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="0e5d8-136">.NET Core 3</span></span>

<span data-ttu-id="0e5d8-137">.NET 上的 gRPC 不支持在 .NET Framework 和 Xamarin 上运行。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="0e5d8-138">[gRPC C# 代码库](https://grpc.io/docs/languages/csharp/quickstart/)是支持 .NET Framework 和 Xamarin 的第三方库。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="0e5d8-139">Microsoft 不支持 gRPC C-core。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="0e5d8-140">Azure 服务</span><span class="sxs-lookup"><span data-stu-id="0e5d8-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="0e5d8-141">Azure Kubernetes 服务 (AKS)</span><span class="sxs-lookup"><span data-stu-id="0e5d8-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="0e5d8-142">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="0e5d8-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="0e5d8-143">&dagger;Azure 应用服务不支持通过 HTTP/2 托管 gRPC。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="0e5d8-144">gRPC-Web 是一种兼容的替代方法。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="0e5d8-145">工作正在进行，以在 Azure 应用服务中提高 HTTP/2 对 gRPC 的支持。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="0e5d8-146">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/9020)。</span><span class="sxs-lookup"><span data-stu-id="0e5d8-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e5d8-147">其他资源</span><span class="sxs-lookup"><span data-stu-id="0e5d8-147">Additional resources</span></span>

* [<span data-ttu-id="0e5d8-148">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="0e5d8-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
