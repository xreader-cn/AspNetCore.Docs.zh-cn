---
title: 使用 C# 的 gRPC 服务
author: juntaoluo
description: 了解使用 C# 编写 gRPC 服务时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061010"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="d32f5-103">使用 C\# 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="d32f5-103">gRPC services with C\#</span></span>

<span data-ttu-id="d32f5-104">本文档概述在 C# 中编写 [gRPC](https://grpc.io/docs/guides/) 应用所需的概念。</span><span class="sxs-lookup"><span data-stu-id="d32f5-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="d32f5-105">此处涵盖的主题适用于基于 [C-core](https://grpc.io/blog/grpc-stacks) 和基于 ASP.NET Core 的 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="d32f5-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="d32f5-106">proto 文件</span><span class="sxs-lookup"><span data-stu-id="d32f5-106">proto file</span></span>

<span data-ttu-id="d32f5-107">gRPC 使用协定优先方法进行 API 开发。</span><span class="sxs-lookup"><span data-stu-id="d32f5-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="d32f5-108">默认情况下，协议缓冲区 (protobuf) 用作接口定义语言 (IDL)。</span><span class="sxs-lookup"><span data-stu-id="d32f5-108">Protocol buffers (protobuf) are used as the Interface Definition Language (IDL) by default.</span></span> <span data-ttu-id="d32f5-109">\*.proto 文件包含：</span><span class="sxs-lookup"><span data-stu-id="d32f5-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="d32f5-110">gRPC 服务的定义。</span><span class="sxs-lookup"><span data-stu-id="d32f5-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="d32f5-111">在客户端与服务器之间发送的消息。</span><span class="sxs-lookup"><span data-stu-id="d32f5-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="d32f5-112">有关 protobuf 文件语法的详细信息，请参阅 <xref:grpc/protobuf>。</span><span class="sxs-lookup"><span data-stu-id="d32f5-112">For more information on the syntax of protobuf files, see <xref:grpc/protobuf>.</span></span>

<span data-ttu-id="d32f5-113">例如，请考虑[开始使用 gRPC 服务](xref:tutorials/grpc/grpc-start)中使用的 greet.proto 文件：</span><span class="sxs-lookup"><span data-stu-id="d32f5-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="d32f5-114">定义 `Greeter` 服务。</span><span class="sxs-lookup"><span data-stu-id="d32f5-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="d32f5-115">`Greeter` 服务定义 `SayHello` 调用。</span><span class="sxs-lookup"><span data-stu-id="d32f5-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="d32f5-116">`SayHello` 发送 `HelloRequest` 消息并接收 `HelloReply` 消息：</span><span class="sxs-lookup"><span data-stu-id="d32f5-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="d32f5-117">将 .proto 文件添加到 C\# 应用</span><span class="sxs-lookup"><span data-stu-id="d32f5-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="d32f5-118">通过将 \*.proto 文件添加到 `<Protobuf>` 项组中，可将该文件包含在项目中：</span><span class="sxs-lookup"><span data-stu-id="d32f5-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="d32f5-119">默认情况下，`<Protobuf>` 引用将生成具体的客户端和服务基类。</span><span class="sxs-lookup"><span data-stu-id="d32f5-119">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="d32f5-120">可使用引用元素的 `GrpcServices` 特性来限制 C# 资产生成。</span><span class="sxs-lookup"><span data-stu-id="d32f5-120">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="d32f5-121">有效 `GrpcServices` 选项如下：</span><span class="sxs-lookup"><span data-stu-id="d32f5-121">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="d32f5-122">`Both`（如果不存在，则为默认值）</span><span class="sxs-lookup"><span data-stu-id="d32f5-122">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="d32f5-123">.proto 文件的 C# 工具支持</span><span class="sxs-lookup"><span data-stu-id="d32f5-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="d32f5-124">需要工具包 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 才能从 \*.proto 文件生成 C# 资产。</span><span class="sxs-lookup"><span data-stu-id="d32f5-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="d32f5-125">生成的资产（文件）：</span><span class="sxs-lookup"><span data-stu-id="d32f5-125">The generated assets (files):</span></span>

* <span data-ttu-id="d32f5-126">在每次生成项目时按需生成。</span><span class="sxs-lookup"><span data-stu-id="d32f5-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="d32f5-127">不会添加到项目中或是签入到源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="d32f5-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="d32f5-128">是包含在 obj 目录中的生成工件。</span><span class="sxs-lookup"><span data-stu-id="d32f5-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="d32f5-129">服务器和客户端项目都需要此包。</span><span class="sxs-lookup"><span data-stu-id="d32f5-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="d32f5-130">`Grpc.AspNetCore` 元包中包含对 `Grpc.Tools` 的引用。</span><span class="sxs-lookup"><span data-stu-id="d32f5-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="d32f5-131">服务器项目可以使用 Visual Studio 中的包管理器或通过将 `<PackageReference>` 添加到项目文件来添加 `Grpc.AspNetCore`：</span><span class="sxs-lookup"><span data-stu-id="d32f5-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="d32f5-132">客户端项目应直接引用 `Grpc.Tools` 以及使用 gRPC 客户端所需的其他包。</span><span class="sxs-lookup"><span data-stu-id="d32f5-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="d32f5-133">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`：</span><span class="sxs-lookup"><span data-stu-id="d32f5-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="d32f5-134">生成的 C# 资产</span><span class="sxs-lookup"><span data-stu-id="d32f5-134">Generated C# assets</span></span>

<span data-ttu-id="d32f5-135">工具包会生成表示在所包含 \*.proto 文件中定义的消息的 C# 类型。</span><span class="sxs-lookup"><span data-stu-id="d32f5-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="d32f5-136">对于服务器端资产，会生成抽象服务基类型。</span><span class="sxs-lookup"><span data-stu-id="d32f5-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="d32f5-137">基类型包含 .proto 文件中包含的所有 gRPC 调用的定义。</span><span class="sxs-lookup"><span data-stu-id="d32f5-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="d32f5-138">创建一个派生自此基类型并为 gRPC 调用实现逻辑的具体服务实现。</span><span class="sxs-lookup"><span data-stu-id="d32f5-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="d32f5-139">对于 `greet.proto`（前面所述的示例），会生成一个包含虚拟 `SayHello` 方法的抽象 `GreeterBase` 类型。</span><span class="sxs-lookup"><span data-stu-id="d32f5-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="d32f5-140">具体实现 `GreeterService` 会替代该方法，并实现处理 gRPC 调用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="d32f5-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="d32f5-141">对于客户端资产，会生成一个具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="d32f5-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="d32f5-142">.proto 文件中的 gRPC 调用会转换为具体类型中的方法，可以进行调用。</span><span class="sxs-lookup"><span data-stu-id="d32f5-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="d32f5-143">对于 `greet.proto`（前面所述的示例），会生成一个 `GreeterClient` 类型。</span><span class="sxs-lookup"><span data-stu-id="d32f5-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="d32f5-144">调用 `GreeterClient.SayHelloAsync` 以发起对服务器的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="d32f5-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="d32f5-145">默认情况下，会为 `<Protobuf>` 项组中包含的每个 \*.proto 文件都生成服务器和客户端资产。</span><span class="sxs-lookup"><span data-stu-id="d32f5-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="d32f5-146">若要确保服务器项目中仅生成服务器资产，请将 `GrpcServices` 属性设置为 `Server`。</span><span class="sxs-lookup"><span data-stu-id="d32f5-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="d32f5-147">同样，该属性在客户端项目中设置为 `Client`。</span><span class="sxs-lookup"><span data-stu-id="d32f5-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d32f5-148">其他资源</span><span class="sxs-lookup"><span data-stu-id="d32f5-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
