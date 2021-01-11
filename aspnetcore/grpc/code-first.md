---
title: 使用 .NET 的代码优先 gRPC 服务和客户端
author: jamesnk
description: 了解使用 .NET 编写代码优先 gRPC 的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880616"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="5183b-103">使用 .NET 的代码优先 gRPC 服务和客户端</span><span class="sxs-lookup"><span data-stu-id="5183b-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="5183b-104">作者：[James Newton-King](https://twitter.com/jamesnk) 和 [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="5183b-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="5183b-105">代码优先 gRPC 使用 .NET 类型来定义服务和消息协定。</span><span class="sxs-lookup"><span data-stu-id="5183b-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="5183b-106">当整个系统使用 .NET 时，代码优先是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="5183b-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="5183b-107">可以在 .NET 服务器和客户端之间共享 .NET 服务和数据协定类型。</span><span class="sxs-lookup"><span data-stu-id="5183b-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="5183b-108">无需在 `.proto` 文件和代码生成过程中定义协定。</span><span class="sxs-lookup"><span data-stu-id="5183b-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="5183b-109">不建议在具有多种语言的 polyglot 系统中使用代码优先。</span><span class="sxs-lookup"><span data-stu-id="5183b-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="5183b-110">.NET 服务和数据协定类型不能与非 .NET 平台一起使用。</span><span class="sxs-lookup"><span data-stu-id="5183b-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="5183b-111">其他平台若要调用使用代码优先编写的 gRPC 服务，必须创建一个与该服务匹配的 `.proto` 协定。</span><span class="sxs-lookup"><span data-stu-id="5183b-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="5183b-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="5183b-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5183b-113">有关 protobuf-net.Grpc 的帮助信息，请访问 [protobuf-net.Grpc 网站](https://protobuf-net.github.io/protobuf-net.Grpc/)或在 [protobuf-net.Grpc GitHub 存储库](https://github.com/protobuf-net/protobuf-net.Grpc)上创建一个问题。</span><span class="sxs-lookup"><span data-stu-id="5183b-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="5183b-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) 是一个社区项目，不受 Microsoft 支持。</span><span class="sxs-lookup"><span data-stu-id="5183b-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="5183b-115">它将对 `Grpc.AspNetCore` 和 `Grpc.Net.Client` 添加代码优先支持。</span><span class="sxs-lookup"><span data-stu-id="5183b-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="5183b-116">它使用通过属性批注的 .NET 类型来定义应用的 gRPC 服务和消息。</span><span class="sxs-lookup"><span data-stu-id="5183b-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="5183b-117">创建代码优先 gRPC 服务的第一步是定义代码协定：</span><span class="sxs-lookup"><span data-stu-id="5183b-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="5183b-118">创建一个将由服务器和客户端共享的新项目。</span><span class="sxs-lookup"><span data-stu-id="5183b-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="5183b-119">添加一个 [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 包引用。</span><span class="sxs-lookup"><span data-stu-id="5183b-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="5183b-120">创建服务和数据协定类型。</span><span class="sxs-lookup"><span data-stu-id="5183b-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="5183b-121">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="5183b-121">The preceding code:</span></span>

* <span data-ttu-id="5183b-122">定义 `HelloRequest` 和 `HelloReply` 消息。</span><span class="sxs-lookup"><span data-stu-id="5183b-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="5183b-123">使用一元 `SayHelloAsync` gRPC 方法定义 `IGreeterService` 协定接口。</span><span class="sxs-lookup"><span data-stu-id="5183b-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="5183b-124">服务协定在服务器上实现并从客户端调用。</span><span class="sxs-lookup"><span data-stu-id="5183b-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="5183b-125">在服务接口上定义的方法必须与某些签名匹配，具体取决于它们是一元、服务器流式处理、客户端流式处理还是双向流式处理。</span><span class="sxs-lookup"><span data-stu-id="5183b-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="5183b-126">有关定义服务协定的详细信息，请参阅 [protobuf-net.Grpc 入门文档](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)。</span><span class="sxs-lookup"><span data-stu-id="5183b-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="5183b-127">创建代码优先 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="5183b-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="5183b-128">若要将 gRPC 代码优先服务添加到 ASP.NET Core 应用，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5183b-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="5183b-129">添加一个 [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 包引用。</span><span class="sxs-lookup"><span data-stu-id="5183b-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="5183b-130">添加一个对共享代码协定项目的引用。</span><span class="sxs-lookup"><span data-stu-id="5183b-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="5183b-131">创建一个新的 `GreeterService.cs` 文件并实现 `IGreeterService` 服务接口：</span><span class="sxs-lookup"><span data-stu-id="5183b-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="5183b-132">更新 `Startup.cs` 文件：</span><span class="sxs-lookup"><span data-stu-id="5183b-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="5183b-133">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="5183b-133">In the preceding code:</span></span>

* <span data-ttu-id="5183b-134">`AddCodeFirstGrpc` 注册启用了代码优先的服务。</span><span class="sxs-lookup"><span data-stu-id="5183b-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="5183b-135">`MapGrpcService<GreeterService>` 添加代码优先的服务终结点。</span><span class="sxs-lookup"><span data-stu-id="5183b-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="5183b-136">使用代码优先和 `.proto` 文件实现的 gRPC 服务可在同一应用中共存。</span><span class="sxs-lookup"><span data-stu-id="5183b-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="5183b-137">所有 gRPC 服务都使用 [gRPC 服务配置](xref:grpc/configuration#configure-services-options)。</span><span class="sxs-lookup"><span data-stu-id="5183b-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="5183b-138">创建代码优先 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="5183b-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="5183b-139">代码优先 gRPC 客户端使用服务协定来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="5183b-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="5183b-140">若要使用代码优先客户端调用 gRPC 服务，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5183b-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="5183b-141">添加一个 [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 包引用。</span><span class="sxs-lookup"><span data-stu-id="5183b-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="5183b-142">添加一个对共享代码协定项目的引用。</span><span class="sxs-lookup"><span data-stu-id="5183b-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="5183b-143">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="5183b-143">The preceding code:</span></span>

* <span data-ttu-id="5183b-144">创建一个 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="5183b-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="5183b-145">使用 `CreateGrpcService<IGreeterService>` 扩展方法从通道创建代码优先客户端。</span><span class="sxs-lookup"><span data-stu-id="5183b-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="5183b-146">使用 `SayHelloAsync` 调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="5183b-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="5183b-147">代码优先 gRPC 客户端是通过通道创建的。</span><span class="sxs-lookup"><span data-stu-id="5183b-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="5183b-148">与常规客户端一样，代码优先客户端使用其[通道配置](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="5183b-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5183b-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="5183b-149">Additional resources</span></span>

* [<span data-ttu-id="5183b-150">protobuf-net.Grpc 网站</span><span class="sxs-lookup"><span data-stu-id="5183b-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="5183b-151">protobuf-net.Grpc GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="5183b-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
