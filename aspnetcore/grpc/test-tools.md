---
title: 用 gRPC 工具实现测试服务
author: jamesnk
description: 了解如何使用 gRPC 工具实现测试服务。 gRPCurl 与 gRPC 服务交互的命令行工具。 gRPCui 是一个交互式 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594360"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="ee497-105">用 gRPC 工具实现测试服务</span><span class="sxs-lookup"><span data-stu-id="ee497-105">Test services with gRPC tools</span></span>

<span data-ttu-id="ee497-106">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ee497-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ee497-107">gRPC 提供了工具，让开发人员可以在不构建客户端应用的情况下测试服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="ee497-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) 是一种命令行工具，可提供与 gRPC 服务的交互。</span><span class="sxs-lookup"><span data-stu-id="ee497-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="ee497-109">[gRPCui](https://github.com/fullstorydev/grpcui) 为 gRPC 添加交互式 Web UI。</span><span class="sxs-lookup"><span data-stu-id="ee497-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="ee497-110">本文介绍如何提供这种交互：</span><span class="sxs-lookup"><span data-stu-id="ee497-110">This article discusses how to:</span></span>

* <span data-ttu-id="ee497-111">下载并安装 gRPCurl 和 gRPCui。</span><span class="sxs-lookup"><span data-stu-id="ee497-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="ee497-112">使用 gRPC ASP.NET Core 应用设置 gRPC 反射。</span><span class="sxs-lookup"><span data-stu-id="ee497-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="ee497-113">使用 `grpcurl` 发现和测试 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="ee497-114">使用 `grpcui` 通过浏览器与 gRPC 服务交互。</span><span class="sxs-lookup"><span data-stu-id="ee497-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="ee497-115">关于 gRPCurl</span><span class="sxs-lookup"><span data-stu-id="ee497-115">About gRPCurl</span></span>

<span data-ttu-id="ee497-116">gRPCurl 是由 gRPC 社区创建的命令行工具。</span><span class="sxs-lookup"><span data-stu-id="ee497-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="ee497-117">其功能包括：</span><span class="sxs-lookup"><span data-stu-id="ee497-117">Its features include:</span></span>

* <span data-ttu-id="ee497-118">调用 gRPC 服务，包括流式服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="ee497-119">使用 [gRPC 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)进行服务发现。</span><span class="sxs-lookup"><span data-stu-id="ee497-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="ee497-120">列出并描述 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="ee497-121">适用于安全 (TLS) 和不安全（纯文本）服务器。</span><span class="sxs-lookup"><span data-stu-id="ee497-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="ee497-122">有关下载和安装 `grpcurl` 的信息，请参阅 [gRPCurl GitHub 主页](https://github.com/fullstorydev/grpcurl#installation)。</span><span class="sxs-lookup"><span data-stu-id="ee497-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="ee497-123">设置 gRPC 反射</span><span class="sxs-lookup"><span data-stu-id="ee497-123">Setup gRPC reflection</span></span>

<span data-ttu-id="ee497-124">`grpcurl` 需要了解服务的 Protobuf 协定，然后才能调用它们。</span><span class="sxs-lookup"><span data-stu-id="ee497-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="ee497-125">有两种方法可以实现此目的：</span><span class="sxs-lookup"><span data-stu-id="ee497-125">There are two ways to do this:</span></span>

* <span data-ttu-id="ee497-126">使用 gRPC 反射来发现服务协定。</span><span class="sxs-lookup"><span data-stu-id="ee497-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="ee497-127">在命令行参数中指定 .proto 文件。</span><span class="sxs-lookup"><span data-stu-id="ee497-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="ee497-128">将 gRPCurl 与 gRPC 反射和服务发现一起使用将更轻松。</span><span class="sxs-lookup"><span data-stu-id="ee497-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="ee497-129">gRPC ASP.NET Core 包含 [gRPC.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 包，因此具有对 gRPC 反射的内置支持。</span><span class="sxs-lookup"><span data-stu-id="ee497-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="ee497-130">在应用中配置反射：</span><span class="sxs-lookup"><span data-stu-id="ee497-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="ee497-131">添加 `Grpc.AspNetCore.Server.Reflection` 包引用。</span><span class="sxs-lookup"><span data-stu-id="ee497-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="ee497-132">在 Startup.cs 中注册反射：</span><span class="sxs-lookup"><span data-stu-id="ee497-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="ee497-133">`AddGrpcReflection` 用于注册启用反射的服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="ee497-134">`MapGrpcReflectionService` 用于添加反射服务终结点。</span><span class="sxs-lookup"><span data-stu-id="ee497-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="ee497-135">使用 `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="ee497-135">Use `grpcurl`</span></span>

<span data-ttu-id="ee497-136">`-help` 参数说明 `grpcurl` 命令行选项：</span><span class="sxs-lookup"><span data-stu-id="ee497-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="ee497-137">发现服务</span><span class="sxs-lookup"><span data-stu-id="ee497-137">Discover services</span></span>

<span data-ttu-id="ee497-138">使用 `describe` 谓词来查看服务器定义的服务：</span><span class="sxs-lookup"><span data-stu-id="ee497-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="ee497-139">上面的示例：</span><span class="sxs-lookup"><span data-stu-id="ee497-139">The preceding example:</span></span>

* <span data-ttu-id="ee497-140">在服务器 `localhost:5001` 上运行 `describe` 谓词。</span><span class="sxs-lookup"><span data-stu-id="ee497-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="ee497-141">打印 gRPC 反射返回的服务和方法。</span><span class="sxs-lookup"><span data-stu-id="ee497-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="ee497-142">`Greeter` 是应用实现的服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="ee497-143">`ServerReflection` 是由`Grpc.AspNetCore.Server.Reflection` 包添加的服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="ee497-144">将 `describe` 与服务、方法或消息名称组合以查看其详细信息：</span><span class="sxs-lookup"><span data-stu-id="ee497-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="ee497-145">调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="ee497-145">Call gRPC services</span></span>

<span data-ttu-id="ee497-146">通过指定服务和方法名称以及表示请求消息的 JSON 参数来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="ee497-147">将 JSON 转换为 Protobuf 并发送到服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="ee497-148">上面的示例：</span><span class="sxs-lookup"><span data-stu-id="ee497-148">The preceding example:</span></span>

* <span data-ttu-id="ee497-149">`-d` 参数使用 JSON 指定请求消息。</span><span class="sxs-lookup"><span data-stu-id="ee497-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="ee497-150">此参数必须位于服务器地址和方法名称之前。</span><span class="sxs-lookup"><span data-stu-id="ee497-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="ee497-151">调用 `greeter.Greeter` 服务上的 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="ee497-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="ee497-152">以 JSON 的形式打印响应消息。</span><span class="sxs-lookup"><span data-stu-id="ee497-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="ee497-153">关于 gRPCui</span><span class="sxs-lookup"><span data-stu-id="ee497-153">About gRPCui</span></span>

<span data-ttu-id="ee497-154">gRPCui 是 gRPC 的交互式 Web UI。</span><span class="sxs-lookup"><span data-stu-id="ee497-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="ee497-155">它基于 gRPCurl 构建，并提供一个 GUI 来发现和测试 gRPC 服务，类似于 Postman 等 HTTP 工具。</span><span class="sxs-lookup"><span data-stu-id="ee497-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="ee497-156">有关下载和安装 `grpcui` 的信息，请参阅 [gRPCui GitHub 主页](https://github.com/fullstorydev/grpcui#installation)。</span><span class="sxs-lookup"><span data-stu-id="ee497-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="ee497-157">使用 `grpcui`</span><span class="sxs-lookup"><span data-stu-id="ee497-157">Using `grpcui`</span></span>

<span data-ttu-id="ee497-158">使用要与之交互的服务器地址作为参数运行 `grpcui`。</span><span class="sxs-lookup"><span data-stu-id="ee497-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="ee497-159">该工具将启动一个带有交互式 Web UI 的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="ee497-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="ee497-160">使用 gRPC 反射自动发现 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ee497-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="ee497-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="ee497-162">Additional resources</span></span>

* [<span data-ttu-id="ee497-163">gRPCurl GitHub 主页</span><span class="sxs-lookup"><span data-stu-id="ee497-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="ee497-164">gRPCui GitHub 主页</span><span class="sxs-lookup"><span data-stu-id="ee497-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="ee497-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="ee497-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
