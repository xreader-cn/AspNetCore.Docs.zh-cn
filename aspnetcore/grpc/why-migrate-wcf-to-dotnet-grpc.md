---
title: 为何要将 WCF 迁移到 ASP.NET Core gRPC
author: markrendle
description: 本文总结了 ASP.NET Core gRPC 非常适合希望迁移到现代架构和平台的 Windows Communication Foundation (WCF) 开发人员的原因。
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058683"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="768cc-103">适用于 Windows Communication Foundation (WCF) 开发人员的 gRPC</span><span class="sxs-lookup"><span data-stu-id="768cc-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="768cc-104">本文总结了 ASP.NET Core gRPC 非常适合希望迁移到现代架构和平台的 Windows Communication Foundation (WCF) 开发人员的原因。</span><span class="sxs-lookup"><span data-stu-id="768cc-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="768cc-105">与 WCF 的比较</span><span class="sxs-lookup"><span data-stu-id="768cc-105">Comparison to WCF</span></span>

<span data-ttu-id="768cc-106">尽管 gRPC 的实现和方法不同，但对于 WCF 开发人员来说，通过 gRPC 开发和使用服务的体验应该是直观的。</span><span class="sxs-lookup"><span data-stu-id="768cc-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="768cc-107">WCF 和 gRPC 是具有相同目标的 RPC（远程过程调用）框架：</span><span class="sxs-lookup"><span data-stu-id="768cc-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="768cc-108">使得可以向客户端和服务器在同一个平台上一样进行编码。</span><span class="sxs-lookup"><span data-stu-id="768cc-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="768cc-109">提供简化的便携网络 API。</span><span class="sxs-lookup"><span data-stu-id="768cc-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="768cc-110">尽管声明接口的过程不同，但两个平台都有声明和实现接口的需求。</span><span class="sxs-lookup"><span data-stu-id="768cc-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="768cc-111">gRPC 支持的许多 RPC 调用类型很好地映射到 WCF 服务可用的绑定。</span><span class="sxs-lookup"><span data-stu-id="768cc-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="768cc-112">有关详细信息和示例，请参阅[将 WCF 解决方案迁移到 gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)。</span><span class="sxs-lookup"><span data-stu-id="768cc-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="768cc-113">gRPC 的优点</span><span class="sxs-lookup"><span data-stu-id="768cc-113">Benefits of gRPC</span></span>

<span data-ttu-id="768cc-114">由于以下原因，gRPC 提供了比其他方法更好的框架。</span><span class="sxs-lookup"><span data-stu-id="768cc-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="768cc-115">性能</span><span class="sxs-lookup"><span data-stu-id="768cc-115">Performance</span></span>

<span data-ttu-id="768cc-116">gRPC 使用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="768cc-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="768cc-117">与 HTTP/1.1、HTTP/2 相比：</span><span class="sxs-lookup"><span data-stu-id="768cc-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="768cc-118">是更小、更快的二进制协议。</span><span class="sxs-lookup"><span data-stu-id="768cc-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="768cc-119">对于要分析的计算机，更有效。</span><span class="sxs-lookup"><span data-stu-id="768cc-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="768cc-120">支持通过单个连接进行多路复用请求。</span><span class="sxs-lookup"><span data-stu-id="768cc-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="768cc-121">多路复用允许在一个连接上发送多个请求，而这些请求不会相互阻塞。</span><span class="sxs-lookup"><span data-stu-id="768cc-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="768cc-122">在 HTTP/1.1 中，阻塞被称为“队头 (HOL) 阻塞”。</span><span class="sxs-lookup"><span data-stu-id="768cc-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="768cc-123">gRPC 使用 Protobuf（一种高效的二进制格式）来序列化消息。</span><span class="sxs-lookup"><span data-stu-id="768cc-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="768cc-124">Protobuf 消息能够：</span><span class="sxs-lookup"><span data-stu-id="768cc-124">Protobuf messages are:</span></span>
* <span data-ttu-id="768cc-125">快速序列化和反序列化。</span><span class="sxs-lookup"><span data-stu-id="768cc-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="768cc-126">使用比基于文本的格式更少的带宽。</span><span class="sxs-lookup"><span data-stu-id="768cc-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="768cc-127">gRPC 是一个很好的解决方案，适用于带宽限制的移动设备和网络。</span><span class="sxs-lookup"><span data-stu-id="768cc-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="768cc-128">互操作性</span><span class="sxs-lookup"><span data-stu-id="768cc-128">Interoperability</span></span>

<span data-ttu-id="768cc-129">对于所有主要的编程语言和平台，都有 gRPC 工具和库，包括 .NET、Java、Python、Go、C++、Node.js、Swift、Dart、Ruby 以及 PHP。</span><span class="sxs-lookup"><span data-stu-id="768cc-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="768cc-130">由于 Protobuf 二进制线格式和每个平台的高效代码生成，开发人员可以构建跨平台、高性能的应用。</span><span class="sxs-lookup"><span data-stu-id="768cc-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="768cc-131">易用性和工作效率</span><span class="sxs-lookup"><span data-stu-id="768cc-131">Usability and productivity</span></span>

<span data-ttu-id="768cc-132">gRPC 是一个全面的 RPC 解决方案。</span><span class="sxs-lookup"><span data-stu-id="768cc-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="768cc-133">它可以跨多种语言和平台一致地工作。</span><span class="sxs-lookup"><span data-stu-id="768cc-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="768cc-134">它还提供很好的工具，许多样板代码都是自动生成的。</span><span class="sxs-lookup"><span data-stu-id="768cc-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="768cc-135">与 WCF 一样，gRPC 自动生成消息和强类型客户端。</span><span class="sxs-lookup"><span data-stu-id="768cc-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="768cc-136">开发人员可以腾出时间来专注于业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="768cc-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="768cc-137">流式处理</span><span class="sxs-lookup"><span data-stu-id="768cc-137">Streaming</span></span>

<span data-ttu-id="768cc-138">gRPC 具有完全双向流式传输，它提供与 WCF 的全双工服务类似的功能。</span><span class="sxs-lookup"><span data-stu-id="768cc-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="768cc-139">gRPC 流式传输可以通过常规的 Internet 连接、负载均衡器和服务网格运行。</span><span class="sxs-lookup"><span data-stu-id="768cc-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="768cc-140">截止时间、超时和取消</span><span class="sxs-lookup"><span data-stu-id="768cc-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="768cc-141">gRPC 允许客户端指定 RPC 完成的最长时间。</span><span class="sxs-lookup"><span data-stu-id="768cc-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="768cc-142">如果超过了指定的截止时间，服务器可以独立于客户端取消操作。</span><span class="sxs-lookup"><span data-stu-id="768cc-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="768cc-143">截止时间和取消可以通过随后的 gRPC 调用进行传播，以帮助实施资源使用限制。</span><span class="sxs-lookup"><span data-stu-id="768cc-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="768cc-144">客户端可以在超过截止时间时停止操作，必要时可以提前停止操作。</span><span class="sxs-lookup"><span data-stu-id="768cc-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="768cc-145">例如，客户端可以因为用户交互而停止操作。</span><span class="sxs-lookup"><span data-stu-id="768cc-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="768cc-146">安全性</span><span class="sxs-lookup"><span data-stu-id="768cc-146">Security</span></span>

<span data-ttu-id="768cc-147">gRPC 可以使用 TLS 和 HTTP/2 在客户端和服务器之间提供端到端的加密连接。</span><span class="sxs-lookup"><span data-stu-id="768cc-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="768cc-148">对客户端证书身份验证的支持进一步提高了客户端和服务器之间的安全性和信任度。</span><span class="sxs-lookup"><span data-stu-id="768cc-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="768cc-149">gRPC 作为 WCF 到 .NET Core 和 .NET 5 的迁移路径</span><span class="sxs-lookup"><span data-stu-id="768cc-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="768cc-150">.NET Core 和 .NET5 标志着 Microsoft 向希望跨一系列平台提供服务的开发人员提供远程通信解决方案的方式发生了转变。</span><span class="sxs-lookup"><span data-stu-id="768cc-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="768cc-151">.NET Core 和 .NET 5 支持[调用 WCF 服务](/dotnet/core/additional-tools/wcf-web-service-reference-guide)，但不会为承载 WCF 提供服务器端支持。</span><span class="sxs-lookup"><span data-stu-id="768cc-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="768cc-152">现代化 WCF 应用有两个推荐的路径：</span><span class="sxs-lookup"><span data-stu-id="768cc-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="768cc-153">gRPC 基于现代技术建立，已经成为 RPC 应用开发人员社区中最受欢迎的选择。</span><span class="sxs-lookup"><span data-stu-id="768cc-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="768cc-154">从 .NET Core 3.0 开始，现代 .NET 平台就对 gRPC 有很好的支持。</span><span class="sxs-lookup"><span data-stu-id="768cc-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="768cc-155">迁移 WCF 服务以使用 gRPC 有助于提供 RPC 功能、性能和现代应用所需的互操作性。</span><span class="sxs-lookup"><span data-stu-id="768cc-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="768cc-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) 是社区共同努力的成果，可为 .NET Core 和 .NET 5 提供托管 WCF 服务支持。</span><span class="sxs-lookup"><span data-stu-id="768cc-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="768cc-157">预览版已经发布，项目正朝着生产就绪的方向努力。</span><span class="sxs-lookup"><span data-stu-id="768cc-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="768cc-158">CoreWCF 仅支持 WCF 功能的一个子集，迁移到使用它的 .NET Framework 应用需要代码更改和测试才能成功。</span><span class="sxs-lookup"><span data-stu-id="768cc-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="768cc-159">如果应用必须保持与调用 WCF 服务的现有客户端的兼容性，那么 CoreWCF 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="768cc-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="768cc-160">入门</span><span class="sxs-lookup"><span data-stu-id="768cc-160">Get started</span></span>

<span data-ttu-id="768cc-161">有关在适用于 WCF 开发人员的 ASP.NET Core 中构建 gRPC 服务的详细指南，请参阅[适用于 WCF 开发人员的 ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)</span><span class="sxs-lookup"><span data-stu-id="768cc-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
