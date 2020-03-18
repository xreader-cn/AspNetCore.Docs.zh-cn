---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 了解使用 C# 编写 gRPC 服务时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649422"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="37843-103">使用 C\# 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="37843-103">gRPC services with C\#</span></span>

<span data-ttu-id="37843-104">本文档概述在 C# 中编写 [gRPC](https://grpc.io/docs/guides/) 应用所需的概念。</span><span class="sxs-lookup"><span data-stu-id="37843-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="37843-105">此处涵盖的主题适用于基于 [C-core](https://grpc.io/blog/grpc-stacks) 和基于 ASP.NET Core 的 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="37843-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="37843-106">proto 文件</span><span class="sxs-lookup"><span data-stu-id="37843-106">proto file</span></span>

<span data-ttu-id="37843-107">gRPC 使用协定优先方法进行 API 开发。</span><span class="sxs-lookup"><span data-stu-id="37843-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="37843-108">默认情况下，协议缓冲区 (protobuf) 用作接口设计语言 (IDL)。</span><span class="sxs-lookup"><span data-stu-id="37843-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="37843-109">\*.proto  文件包含：</span><span class="sxs-lookup"><span data-stu-id="37843-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="37843-110">gRPC 服务的定义。</span><span class="sxs-lookup"><span data-stu-id="37843-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="37843-111">在客户端与服务器之间发送的消息。</span><span class="sxs-lookup"><span data-stu-id="37843-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="37843-112">有关 protobuf 文件的语法的详细信息，请参阅[官方文档 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)。</span><span class="sxs-lookup"><span data-stu-id="37843-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="37843-113">例如，请考虑[开始使用 gRPC 服务](xref:tutorials/grpc/grpc-start)中使用的 greet.proto  文件：</span><span class="sxs-lookup"><span data-stu-id="37843-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="37843-114">定义 `Greeter` 服务。</span><span class="sxs-lookup"><span data-stu-id="37843-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="37843-115">`Greeter` 服务定义 `SayHello` 调用。</span><span class="sxs-lookup"><span data-stu-id="37843-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="37843-116">`SayHello` 发送 `HelloRequest` 消息并接收 `HelloReply` 消息：</span><span class="sxs-lookup"><span data-stu-id="37843-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="37843-117">将 .proto 文件添加到 C\# 应用</span><span class="sxs-lookup"><span data-stu-id="37843-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="37843-118">通过将 \*.proto  文件添加到 `<Protobuf>` 项组中，可将该文件包含在项目中：</span><span class="sxs-lookup"><span data-stu-id="37843-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="37843-119">.proto 文件的 C# 工具支持</span><span class="sxs-lookup"><span data-stu-id="37843-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="37843-120">需要工具包 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 才能从 \*.proto  文件生成 C# 资产。</span><span class="sxs-lookup"><span data-stu-id="37843-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="37843-121">生成的资产（文件）：</span><span class="sxs-lookup"><span data-stu-id="37843-121">The generated assets (files):</span></span>

* <span data-ttu-id="37843-122">在每次生成项目时按需生成。</span><span class="sxs-lookup"><span data-stu-id="37843-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="37843-123">不会添加到项目中或是签入到源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="37843-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="37843-124">是包含在 obj  目录中的生成工件。</span><span class="sxs-lookup"><span data-stu-id="37843-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="37843-125">服务器和客户端项目都需要此包。</span><span class="sxs-lookup"><span data-stu-id="37843-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="37843-126">`Grpc.AspNetCore` 元包中包含对 `Grpc.Tools` 的引用。</span><span class="sxs-lookup"><span data-stu-id="37843-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="37843-127">服务器项目可以使用 Visual Studio 中的包管理器或通过将 `<PackageReference>` 添加到项目文件来添加 `Grpc.AspNetCore`：</span><span class="sxs-lookup"><span data-stu-id="37843-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="37843-128">客户端项目应直接引用 `Grpc.Tools` 以及使用 gRPC 客户端所需的其他包。</span><span class="sxs-lookup"><span data-stu-id="37843-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="37843-129">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`：</span><span class="sxs-lookup"><span data-stu-id="37843-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="37843-130">生成的 C# 资产</span><span class="sxs-lookup"><span data-stu-id="37843-130">Generated C# assets</span></span>

<span data-ttu-id="37843-131">工具包会生成表示在所包含 \*.proto  文件中定义的消息的 C# 类型。</span><span class="sxs-lookup"><span data-stu-id="37843-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="37843-132">对于服务器端资产，会生成抽象服务基类型。</span><span class="sxs-lookup"><span data-stu-id="37843-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="37843-133">基类型包含 .proto  文件中包含的所有 gRPC 调用的定义。</span><span class="sxs-lookup"><span data-stu-id="37843-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="37843-134">创建一个派生自此基类型并为 gRPC 调用实现逻辑的具体服务实现。</span><span class="sxs-lookup"><span data-stu-id="37843-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="37843-135">对于 `greet.proto`（前面所述的示例），会生成一个包含虚拟 `SayHello` 方法的抽象 `GreeterBase` 类型。</span><span class="sxs-lookup"><span data-stu-id="37843-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="37843-136">具体实现 `GreeterService` 会替代该方法，并实现处理 gRPC 调用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="37843-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="37843-137">对于客户端资产，会生成一个具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="37843-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="37843-138">.proto  文件中的 gRPC 调用会转换为具体类型中的方法，可以进行调用。</span><span class="sxs-lookup"><span data-stu-id="37843-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="37843-139">对于 `greet.proto`（前面所述的示例），会生成一个 `GreeterClient` 类型。</span><span class="sxs-lookup"><span data-stu-id="37843-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="37843-140">调用 `GreeterClient.SayHelloAsync` 以发起对服务器的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="37843-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="37843-141">默认情况下，会为 `<Protobuf>` 项组中包含的每个 \*.proto  文件都生成服务器和客户端资产。</span><span class="sxs-lookup"><span data-stu-id="37843-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="37843-142">若要确保服务器项目中仅生成服务器资产，请将 `GrpcServices` 属性设置为 `Server`。</span><span class="sxs-lookup"><span data-stu-id="37843-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="37843-143">同样，该属性在客户端项目中设置为 `Client`。</span><span class="sxs-lookup"><span data-stu-id="37843-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="37843-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="37843-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
