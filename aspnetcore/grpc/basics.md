---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 了解用C#编写 gRPC services 的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925190"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="0c130-103">gRPC 服务与 C\#</span><span class="sxs-lookup"><span data-stu-id="0c130-103">gRPC services with C\#</span></span>

<span data-ttu-id="0c130-104">本文档概述了在中C#编写[gRPC](https://grpc.io/docs/guides/)应用程序所需的概念。</span><span class="sxs-lookup"><span data-stu-id="0c130-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="0c130-105">本文介绍的主题适用于基于[C](https://grpc.io/blog/grpc-stacks)和 ASP.NET Core 的 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="0c130-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="0c130-106">proto 文件</span><span class="sxs-lookup"><span data-stu-id="0c130-106">proto file</span></span>

<span data-ttu-id="0c130-107">gRPC 使用协定优先方法进行 API 开发。</span><span class="sxs-lookup"><span data-stu-id="0c130-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="0c130-108">默认情况下，协议缓冲区（protobuf）用作接口设计语言（IDL）。</span><span class="sxs-lookup"><span data-stu-id="0c130-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="0c130-109">*\*的 proto*文件包含：</span><span class="sxs-lookup"><span data-stu-id="0c130-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="0c130-110">GRPC 服务的定义。</span><span class="sxs-lookup"><span data-stu-id="0c130-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="0c130-111">客户端和服务器之间发送的消息。</span><span class="sxs-lookup"><span data-stu-id="0c130-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="0c130-112">有关 protobuf 文件语法的详细信息，请参阅[官方文档（protobuf）](https://developers.google.com/protocol-buffers/docs/proto3)。</span><span class="sxs-lookup"><span data-stu-id="0c130-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="0c130-113">例如，请考虑[gRPC 服务入门](xref:tutorials/grpc/grpc-start)中使用的*fibonacci*文件：</span><span class="sxs-lookup"><span data-stu-id="0c130-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="0c130-114">定义 `Greeter` 服务。</span><span class="sxs-lookup"><span data-stu-id="0c130-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="0c130-115">`Greeter` 服务定义 `SayHello` 调用。</span><span class="sxs-lookup"><span data-stu-id="0c130-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="0c130-116">`SayHello` 发送 `HelloRequest` 消息并收到 `HelloReply` 消息：</span><span class="sxs-lookup"><span data-stu-id="0c130-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="0c130-117">将一个 proto 文件添加到 C\# 应用</span><span class="sxs-lookup"><span data-stu-id="0c130-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="0c130-118">在项目中，将 *\*proto*文件添加到 `<Protobuf>` 项组：</span><span class="sxs-lookup"><span data-stu-id="0c130-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="0c130-119">.proto 文件的 C# 工具支持</span><span class="sxs-lookup"><span data-stu-id="0c130-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="0c130-120">需要工具包[Grpc](https://www.nuget.org/packages/Grpc.Tools/)才能从C# *\*proto*文件生成资产。</span><span class="sxs-lookup"><span data-stu-id="0c130-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="0c130-121">生成的资产（文件）：</span><span class="sxs-lookup"><span data-stu-id="0c130-121">The generated assets (files):</span></span>

* <span data-ttu-id="0c130-122">每次生成项目时都按需生成。</span><span class="sxs-lookup"><span data-stu-id="0c130-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="0c130-123">不会添加到项目中，也不会签入源控件。</span><span class="sxs-lookup"><span data-stu-id="0c130-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="0c130-124">是包含在*obj*目录中的生成项目。</span><span class="sxs-lookup"><span data-stu-id="0c130-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="0c130-125">服务器项目和客户端项目都需要此包。</span><span class="sxs-lookup"><span data-stu-id="0c130-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="0c130-126">`Grpc.AspNetCore` 元包包括对 `Grpc.Tools`的引用。</span><span class="sxs-lookup"><span data-stu-id="0c130-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="0c130-127">服务器项目可以使用 Visual Studio 中的包管理器或通过将 `<PackageReference>` 添加到项目文件来添加 `Grpc.AspNetCore`：</span><span class="sxs-lookup"><span data-stu-id="0c130-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="0c130-128">客户端项目应直接引用 `Grpc.Tools` 使用 gRPC 客户端所需的其他包。</span><span class="sxs-lookup"><span data-stu-id="0c130-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="0c130-129">运行时不需要工具包，因此，该依赖项标记为 `PrivateAssets="All"`：</span><span class="sxs-lookup"><span data-stu-id="0c130-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="0c130-130">生成C#的资产</span><span class="sxs-lookup"><span data-stu-id="0c130-130">Generated C# assets</span></span>

<span data-ttu-id="0c130-131">工具包将生成表示C#在包含的 *\*proto*文件中定义的消息的类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="0c130-132">对于服务器端资产，会生成抽象服务基类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="0c130-133">基类型包含*proto*文件中包含的所有 gRPC 调用的定义。</span><span class="sxs-lookup"><span data-stu-id="0c130-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="0c130-134">创建一个派生自此基类型的具体服务实现，并为 gRPC 调用实现逻辑。</span><span class="sxs-lookup"><span data-stu-id="0c130-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="0c130-135">对于 `greet.proto`（前面所述的示例），将生成一个包含虚拟 `SayHello` 方法的抽象 `GreeterBase` 类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="0c130-136">具体的实现 `GreeterService` 重写方法，并实现处理 gRPC 调用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0c130-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="0c130-137">对于客户端资产，会生成具体的客户端类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="0c130-138">*Proto*文件中的 gRPC 调用被转换为具体类型上的方法，可以调用该类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="0c130-139">在 `greet.proto`中，将生成一个具体 `GreeterClient` 类型。</span><span class="sxs-lookup"><span data-stu-id="0c130-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="0c130-140">调用 `GreeterClient.SayHelloAsync` 以启动对服务器的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="0c130-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="0c130-141">默认情况下，将为 `<Protobuf>` 项组中包含的每个 *\*proto*文件生成服务器和客户端资产。</span><span class="sxs-lookup"><span data-stu-id="0c130-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="0c130-142">若要确保服务器项目中仅生成服务器资产，则 `GrpcServices` 属性设置为 `Server`。</span><span class="sxs-lookup"><span data-stu-id="0c130-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="0c130-143">同样，特性设置为在客户端项目中 `Client`。</span><span class="sxs-lookup"><span data-stu-id="0c130-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="0c130-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="0c130-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
