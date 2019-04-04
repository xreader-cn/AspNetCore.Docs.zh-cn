---
title: ASP.NET Core 上 gRPC 的简介
author: juntaoluo
description: 了解使用 Kestrel 服务器和 ASP.NET Core 堆栈的 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142410"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a><span data-ttu-id="cc31c-103">ASP.NET Core 上 gRPC 的简介</span><span class="sxs-lookup"><span data-stu-id="cc31c-103">Introduction to gRPC on ASP.NET Core</span></span>

<span data-ttu-id="cc31c-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="cc31c-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="cc31c-105">[gRPC](https://grpc.io/docs/guides/) 是一种与语言无关的高性能远程过程调用 (RPC) 框架。</span><span class="sxs-lookup"><span data-stu-id="cc31c-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span> <span data-ttu-id="cc31c-106">有关 gRPC 基础知识的详细信息，请参阅 [gRPC 文档页](https://grpc.io/docs/)。</span><span class="sxs-lookup"><span data-stu-id="cc31c-106">For more on gRPC fundamentals, see the [gRPC documentation page](https://grpc.io/docs/).</span></span>

<span data-ttu-id="cc31c-107">gRPC 的主要优点是：</span><span class="sxs-lookup"><span data-stu-id="cc31c-107">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="cc31c-108">现代高性能轻量级 RPC 框架。</span><span class="sxs-lookup"><span data-stu-id="cc31c-108">Modern high-performance lightweight RPC framework.</span></span>
* <span data-ttu-id="cc31c-109">协定优先 API 开发，默认使用协议缓冲区，允许与语言无关的实现。</span><span class="sxs-lookup"><span data-stu-id="cc31c-109">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="cc31c-110">可用于多种语言的工具，以生成强类型服务器和客户端。</span><span class="sxs-lookup"><span data-stu-id="cc31c-110">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="cc31c-111">支持客户端、服务器和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="cc31c-111">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="cc31c-112">使用 Protobuf 二进制序列化减少对网络的使用。</span><span class="sxs-lookup"><span data-stu-id="cc31c-112">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="cc31c-113">这些优点使 gRPC 适用于：</span><span class="sxs-lookup"><span data-stu-id="cc31c-113">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="cc31c-114">效率至关重要的轻量级微服务。</span><span class="sxs-lookup"><span data-stu-id="cc31c-114">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="cc31c-115">需要多种语言用于开发的 Polyglot 系统。</span><span class="sxs-lookup"><span data-stu-id="cc31c-115">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="cc31c-116">需要处理流式处理请求或响应的点对点实时服务。</span><span class="sxs-lookup"><span data-stu-id="cc31c-116">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

<span data-ttu-id="cc31c-117">虽然 C# 实现目前在官方 [ gRPC 页](https://grpc.io/docs/quickstart/csharp.html)上可用，但当前实现依赖于用 C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)) 编写的本机库。</span><span class="sxs-lookup"><span data-stu-id="cc31c-117">While a C# implementation is currently available on the official [gRPC page](https://grpc.io/docs/quickstart/csharp.html), the current implementation relies on the native library written in C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span></span> <span data-ttu-id="cc31c-118">目前正在开展工作，以提供基于 Kestrel HTTP 服务器和完全托管的 ASP.NET Core 堆栈的新实现。</span><span class="sxs-lookup"><span data-stu-id="cc31c-118">Work is currently in progress to provide a new implementation based on the Kestrel HTTP server and the ASP.NET Core stack that is fully managed.</span></span> <span data-ttu-id="cc31c-119">以下文档介绍了使用此新实现生成 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="cc31c-119">The following documents provide an introduction to building gRPC services with this new implementation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc31c-120">其他资源</span><span class="sxs-lookup"><span data-stu-id="cc31c-120">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>