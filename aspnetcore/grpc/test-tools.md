---
title: 在 ASP.NET Core 中使用 gRPCurl 来测试 gRPC 服务
author: jamesnk
description: 了解如何使用 gRPC 工具实现测试服务。 gRPCurl 与 gRPC 服务交互的命令行工具。 gRPCui 是一个交互式 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058787"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="770fd-105">在 ASP.NET Core 中使用 gRPCurl 来测试 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="770fd-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="770fd-106">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="770fd-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="770fd-107">此工具适用于 gRPC，让开发人员可以在不生成客户端应用的情况下测试服务：</span><span class="sxs-lookup"><span data-stu-id="770fd-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="770fd-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) 是一种命令行工具，可提供与 gRPC 服务的交互。</span><span class="sxs-lookup"><span data-stu-id="770fd-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="770fd-109">[gRPCui](https://github.com/fullstorydev/grpcui) 基于 gRPCurl，并为 gRPC 添加了交互式 Web UI，类似于 Postman 和 Swagger UI 等工具。</span><span class="sxs-lookup"><span data-stu-id="770fd-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="770fd-110">本文介绍如何提供这种交互：</span><span class="sxs-lookup"><span data-stu-id="770fd-110">This article discusses how to:</span></span>

* <span data-ttu-id="770fd-111">下载并安装 gRPCurl 和 gRPCui。</span><span class="sxs-lookup"><span data-stu-id="770fd-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="770fd-112">使用 gRPC ASP.NET Core 应用设置 gRPC 反射。</span><span class="sxs-lookup"><span data-stu-id="770fd-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="770fd-113">使用 `grpcurl` 发现和测试 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="770fd-114">使用 `grpcui` 通过浏览器与 gRPC 服务交互。</span><span class="sxs-lookup"><span data-stu-id="770fd-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="770fd-115">关于 gRPCurl</span><span class="sxs-lookup"><span data-stu-id="770fd-115">About gRPCurl</span></span>

<span data-ttu-id="770fd-116">gRPCurl 是由 gRPC 社区创建的命令行工具。</span><span class="sxs-lookup"><span data-stu-id="770fd-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="770fd-117">其功能包括：</span><span class="sxs-lookup"><span data-stu-id="770fd-117">Its features include:</span></span>

* <span data-ttu-id="770fd-118">调用 gRPC 服务，包括流式服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="770fd-119">使用 [gRPC 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)进行服务发现。</span><span class="sxs-lookup"><span data-stu-id="770fd-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="770fd-120">列出并描述 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="770fd-121">适用于安全 (TLS) 和不安全（纯文本）服务器。</span><span class="sxs-lookup"><span data-stu-id="770fd-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="770fd-122">有关下载和安装 `grpcurl` 的信息，请参阅 [gRPCurl GitHub 主页](https://github.com/fullstorydev/grpcurl#installation)。</span><span class="sxs-lookup"><span data-stu-id="770fd-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![gRPCurl 命令行](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="770fd-124">设置 gRPC 反射</span><span class="sxs-lookup"><span data-stu-id="770fd-124">Set up gRPC reflection</span></span>

<span data-ttu-id="770fd-125">`grpcurl` 必须了解服务的 Protobuf 协定，然后才能调用它们。</span><span class="sxs-lookup"><span data-stu-id="770fd-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="770fd-126">有两种方法可以实现此目的：</span><span class="sxs-lookup"><span data-stu-id="770fd-126">There are two ways to do this:</span></span>

* <span data-ttu-id="770fd-127">在服务器上设置 [gRPC 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)。</span><span class="sxs-lookup"><span data-stu-id="770fd-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="770fd-128">gRPCurl 会自动发现服务协定。</span><span class="sxs-lookup"><span data-stu-id="770fd-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="770fd-129">在 gRPCurl 的命令行参数中指定 `.proto` 文件。</span><span class="sxs-lookup"><span data-stu-id="770fd-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="770fd-130">结合使用 gRPCurl 和 gRPC 反射会更加轻松。</span><span class="sxs-lookup"><span data-stu-id="770fd-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="770fd-131">gRPC 反射向应用添加了新的 gRPC 服务，客户端可以调用该服务来发现服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="770fd-132">gRPC ASP.NET Core 包含 [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 包，因此具有对 gRPC 反射的内置支持。</span><span class="sxs-lookup"><span data-stu-id="770fd-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="770fd-133">在应用中配置反射：</span><span class="sxs-lookup"><span data-stu-id="770fd-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="770fd-134">添加 `Grpc.AspNetCore.Server.Reflection` 包引用。</span><span class="sxs-lookup"><span data-stu-id="770fd-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="770fd-135">在 `Startup.cs` 中注册反射：</span><span class="sxs-lookup"><span data-stu-id="770fd-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="770fd-136">`AddGrpcReflection` 用于注册启用反射的服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="770fd-137">`MapGrpcReflectionService` 用于添加反射服务终结点。</span><span class="sxs-lookup"><span data-stu-id="770fd-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="770fd-138">设置 gRPC 反射后：</span><span class="sxs-lookup"><span data-stu-id="770fd-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="770fd-139">gRPC 反射服务将添加到服务器应用。</span><span class="sxs-lookup"><span data-stu-id="770fd-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="770fd-140">支持 gRPC 反射的客户端应用可以调用反射服务来发现服务器托管的服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="770fd-141">仍从客户端调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="770fd-142">反射只支持服务发现，不会绕过服务器端安全设置。</span><span class="sxs-lookup"><span data-stu-id="770fd-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="770fd-143">受[身份验证和授权](xref:grpc/authn-and-authz)保护的终结点需要调用方传递凭据才能成功调用终结点。</span><span class="sxs-lookup"><span data-stu-id="770fd-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="770fd-144">使用 `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="770fd-144">Use `grpcurl`</span></span>

<span data-ttu-id="770fd-145">`-help` 参数说明 `grpcurl` 命令行选项：</span><span class="sxs-lookup"><span data-stu-id="770fd-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="770fd-146">发现服务</span><span class="sxs-lookup"><span data-stu-id="770fd-146">Discover services</span></span>

<span data-ttu-id="770fd-147">使用 `describe` 谓词来查看服务器定义的服务：</span><span class="sxs-lookup"><span data-stu-id="770fd-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
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

<span data-ttu-id="770fd-148">上面的示例：</span><span class="sxs-lookup"><span data-stu-id="770fd-148">The preceding example:</span></span>

* <span data-ttu-id="770fd-149">在服务器 `localhost:5001` 上运行 `describe` 谓词。</span><span class="sxs-lookup"><span data-stu-id="770fd-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="770fd-150">打印 gRPC 反射返回的服务和方法。</span><span class="sxs-lookup"><span data-stu-id="770fd-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="770fd-151">`Greeter` 是应用实现的服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="770fd-152">`ServerReflection` 是由`Grpc.AspNetCore.Server.Reflection` 包添加的服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="770fd-153">结合 `describe` 与服务、方法或消息名称，以查看其详细信息：</span><span class="sxs-lookup"><span data-stu-id="770fd-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="770fd-154">调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="770fd-154">Call gRPC services</span></span>

<span data-ttu-id="770fd-155">指定服务和方法名称以及表示请求消息的 JSON 参数，以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="770fd-156">将 JSON 转换为 Protobuf 并发送到服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="770fd-157">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="770fd-157">In the preceding example:</span></span>

* <span data-ttu-id="770fd-158">`-d` 参数使用 JSON 来指定请求消息。</span><span class="sxs-lookup"><span data-stu-id="770fd-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="770fd-159">此参数必须位于服务器地址和方法名称之前。</span><span class="sxs-lookup"><span data-stu-id="770fd-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="770fd-160">调用 `greeter.Greeter` 服务上的 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="770fd-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="770fd-161">以 JSON 的形式打印响应消息。</span><span class="sxs-lookup"><span data-stu-id="770fd-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="770fd-162">关于 gRPCui</span><span class="sxs-lookup"><span data-stu-id="770fd-162">About gRPCui</span></span>

<span data-ttu-id="770fd-163">gRPCui 是 gRPC 的交互式 Web UI。</span><span class="sxs-lookup"><span data-stu-id="770fd-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="770fd-164">它基于 gRPCurl，并提供一个 GUI 来发现和测试 gRPC 服务，类似于 Postman 或 Swagger UI 等 HTTP 工具。</span><span class="sxs-lookup"><span data-stu-id="770fd-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="770fd-165">有关下载和安装 `grpcui` 的信息，请参阅 [gRPCui GitHub 主页](https://github.com/fullstorydev/grpcui#installation)。</span><span class="sxs-lookup"><span data-stu-id="770fd-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="770fd-166">使用 `grpcui`</span><span class="sxs-lookup"><span data-stu-id="770fd-166">Using `grpcui`</span></span>

<span data-ttu-id="770fd-167">使用要与之交互的服务器地址作为参数来运行 `grpcui`：</span><span class="sxs-lookup"><span data-stu-id="770fd-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="770fd-168">该工具将启动一个带有交互式 Web UI 的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="770fd-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="770fd-169">使用 gRPC 反射自动发现 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="770fd-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="770fd-171">其他资源</span><span class="sxs-lookup"><span data-stu-id="770fd-171">Additional resources</span></span>

* [<span data-ttu-id="770fd-172">gRPCurl GitHub 主页</span><span class="sxs-lookup"><span data-stu-id="770fd-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="770fd-173">gRPCui GitHub 主页</span><span class="sxs-lookup"><span data-stu-id="770fd-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
