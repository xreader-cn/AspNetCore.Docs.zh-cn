---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 编写使用 gRPC 服务时了解的基本概念C#。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 7c5ecf21124414b21f5c36b76e90bde67ac1f958
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672666"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="5da9c-103">gRPC 服务与 C\#</span><span class="sxs-lookup"><span data-stu-id="5da9c-103">gRPC services with C\#</span></span>

<span data-ttu-id="5da9c-104">本文档概述了用于编写所需的概念[gRPC](https://grpc.io/docs/guides/)中的应用C#。</span><span class="sxs-lookup"><span data-stu-id="5da9c-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="5da9c-105">此处介绍的主题应用于两个[C 核心](https://grpc.io/blog/grpc-stacks)-基于和 ASP.NET Core 基于 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="5da9c-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="5da9c-106">proto 文件</span><span class="sxs-lookup"><span data-stu-id="5da9c-106">proto file</span></span>

<span data-ttu-id="5da9c-107">gRPC 使用 API 开发的约定优先方法。</span><span class="sxs-lookup"><span data-stu-id="5da9c-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="5da9c-108">默认情况下作为接口设计语言 (IDL) 使用协议缓冲区 (protobuf)。</span><span class="sxs-lookup"><span data-stu-id="5da9c-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="5da9c-109">*.Proto*文件包含：</span><span class="sxs-lookup"><span data-stu-id="5da9c-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="5da9c-110">GRPC 服务的定义。</span><span class="sxs-lookup"><span data-stu-id="5da9c-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="5da9c-111">客户端和服务器之间发送的消息。</span><span class="sxs-lookup"><span data-stu-id="5da9c-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="5da9c-112">有关语法 protobuf 文件的详细信息，请参阅[官方文档 (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)。</span><span class="sxs-lookup"><span data-stu-id="5da9c-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="5da9c-113">例如，考虑*greet.proto*文件中使用[gRPC 服务入门](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="5da9c-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="5da9c-114">定义`Greeter`服务。</span><span class="sxs-lookup"><span data-stu-id="5da9c-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="5da9c-115">`Greeter`服务定义`SayHello`调用。</span><span class="sxs-lookup"><span data-stu-id="5da9c-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="5da9c-116">`SayHello` 将发送`HelloRequest`消息，并接收`HelloResponse`消息：</span><span class="sxs-lookup"><span data-stu-id="5da9c-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="5da9c-117">将.proto 文件添加到 C\#应用</span><span class="sxs-lookup"><span data-stu-id="5da9c-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="5da9c-118">*.Proto*情况下将其添加到项目中包含文件`<Protobuf>`项组：</span><span class="sxs-lookup"><span data-stu-id="5da9c-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="5da9c-119">C#.Proto 文件的工具支持</span><span class="sxs-lookup"><span data-stu-id="5da9c-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="5da9c-120">工具程序包[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)生成所需C#中的资产 *.proto*文件。</span><span class="sxs-lookup"><span data-stu-id="5da9c-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="5da9c-121">将生成的资产 （文件）：</span><span class="sxs-lookup"><span data-stu-id="5da9c-121">The generated assets (files):</span></span>

* <span data-ttu-id="5da9c-122">生成按需在每次生成项目。</span><span class="sxs-lookup"><span data-stu-id="5da9c-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="5da9c-123">不会添加到项目或签入源控件。</span><span class="sxs-lookup"><span data-stu-id="5da9c-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="5da9c-124">是生成项目中包含*obj*目录。</span><span class="sxs-lookup"><span data-stu-id="5da9c-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="5da9c-125">此包是所需的服务器和客户端项目。</span><span class="sxs-lookup"><span data-stu-id="5da9c-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="5da9c-126">`Grpc.Tools` 可以通过使用 Visual Studio 中的包管理器或添加添加`<PackageReference>`的项目文件：</span><span class="sxs-lookup"><span data-stu-id="5da9c-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

<span data-ttu-id="5da9c-127">工具包不需要在运行时，因此将依赖关系将标有`PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="5da9c-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="5da9c-128">生成C#资产</span><span class="sxs-lookup"><span data-stu-id="5da9c-128">Generated C# assets</span></span>

<span data-ttu-id="5da9c-129">工具包生成C#类型表示定义中所包含的消息 *.proto*文件。</span><span class="sxs-lookup"><span data-stu-id="5da9c-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="5da9c-130">对于服务器端的资产会生成抽象服务基类型。</span><span class="sxs-lookup"><span data-stu-id="5da9c-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="5da9c-131">基类型包含所有 gRPC 调用中包含的定义 *.proto*文件。</span><span class="sxs-lookup"><span data-stu-id="5da9c-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="5da9c-132">创建此基类型派生并实现 gRPC 调用逻辑的具体的服务实现。</span><span class="sxs-lookup"><span data-stu-id="5da9c-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="5da9c-133">有关`greet.proto`，该示例前面所述，一个抽象`GreeterBase`类型，它包含一个虚拟`SayHello`生成方法。</span><span class="sxs-lookup"><span data-stu-id="5da9c-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="5da9c-134">具体实现`GreeterService`重写方法，并实现处理 gRPC 调用逻辑。</span><span class="sxs-lookup"><span data-stu-id="5da9c-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="5da9c-135">对于客户端的资产会生成具体的客户端类型。</span><span class="sxs-lookup"><span data-stu-id="5da9c-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="5da9c-136">调用 gRPC *.proto*文件转换为具体类型，可以调用的方法。</span><span class="sxs-lookup"><span data-stu-id="5da9c-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="5da9c-137">有关`greet.proto`，该示例前面所述，为具体`GreeterClient`生成的类型。</span><span class="sxs-lookup"><span data-stu-id="5da9c-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="5da9c-138">调用`GreeterClient.SayHello`启动到服务器的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="5da9c-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

<span data-ttu-id="5da9c-139">默认情况下，为每个生成服务器和客户端资产 *.proto*文件中包含`<Protobuf>`项组。</span><span class="sxs-lookup"><span data-stu-id="5da9c-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="5da9c-140">若要确保只有服务器资产在服务器项目中生成`GrpcServices`属性设置为`Server`。</span><span class="sxs-lookup"><span data-stu-id="5da9c-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

<span data-ttu-id="5da9c-141">同样，该属性设置为`Client`在客户端项目中。</span><span class="sxs-lookup"><span data-stu-id="5da9c-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5da9c-142">其他资源</span><span class="sxs-lookup"><span data-stu-id="5da9c-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
