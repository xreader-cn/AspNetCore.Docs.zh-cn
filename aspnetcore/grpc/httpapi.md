---
title: 从 gRPC 创建 JSON Web API
author: jamesnk
description: 了解如何为 gRPC 服务创建 JSON HTTP API。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: fa4e7489920338344b78874690e64d4080b5a719
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805578"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="a9363-103">从 gRPC 创建 JSON Web API</span><span class="sxs-lookup"><span data-stu-id="a9363-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="a9363-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a9363-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a9363-105">gRPC HTTP API 是一个试验性项目，而不是已提交的产品。</span><span class="sxs-lookup"><span data-stu-id="a9363-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="a9363-106">我们希望：</span><span class="sxs-lookup"><span data-stu-id="a9363-106">We want to:</span></span>
>
> * <span data-ttu-id="a9363-107">测试我们为 gRPC 服务创建 JSON Web API 的方法是否有效。</span><span class="sxs-lookup"><span data-stu-id="a9363-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="a9363-108">获取有关此方法是否对 .NET 开发者有用的反馈。</span><span class="sxs-lookup"><span data-stu-id="a9363-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="a9363-109">请[提供反馈](https://github.com/grpc/grpc-dotnet/issues/167)，确保我们生成的内容受到开发者欢迎并能帮助他们提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="a9363-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="a9363-110">gRPC 是一种在应用之间进行通信的新方式。</span><span class="sxs-lookup"><span data-stu-id="a9363-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="a9363-111">gRPC 使用 HTTP/2、流式传输、Protobuf 和消息协定来创建高性能的实时服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="a9363-112">gRPC 有一个限制，即不是所有平台都可以使用它。</span><span class="sxs-lookup"><span data-stu-id="a9363-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="a9363-113">浏览器并不完全支持 HTTP/2，这使得 REST 和 JSON 成为将数据引入浏览器应用的主要方式。</span><span class="sxs-lookup"><span data-stu-id="a9363-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="a9363-114">即使有 gRPC 带来的好处，REST 和 JSON 在新式应用中也发挥着重要作用。</span><span class="sxs-lookup"><span data-stu-id="a9363-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="a9363-115">构建 gRPC 和 JSON Web API 给应用开发增加了不必要的开销。</span><span class="sxs-lookup"><span data-stu-id="a9363-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="a9363-116">本文讨论如何使用 gRPC 服务创建 JSON Web API。</span><span class="sxs-lookup"><span data-stu-id="a9363-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="a9363-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="a9363-117">gRPC HTTP API</span></span>

<span data-ttu-id="a9363-118">gRPC HTTP API 是为 gRPC 服务创建 RESTful JSON API 的 ASP.NET Core 的实验性扩展。</span><span class="sxs-lookup"><span data-stu-id="a9363-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="a9363-119">配置 gRPC HTTP API 后，应用可以使用熟悉的 HTTP 概念调用 gRPC 服务：</span><span class="sxs-lookup"><span data-stu-id="a9363-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="a9363-120">HTTP 谓词</span><span class="sxs-lookup"><span data-stu-id="a9363-120">HTTP verbs</span></span>
* <span data-ttu-id="a9363-121">URL 参数绑定</span><span class="sxs-lookup"><span data-stu-id="a9363-121">URL parameter binding</span></span>
* <span data-ttu-id="a9363-122">JSON 请求/响应</span><span class="sxs-lookup"><span data-stu-id="a9363-122">JSON requests/responses</span></span>

<span data-ttu-id="a9363-123">gRPC 仍然可以用来调用服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="a9363-124">使用情况</span><span class="sxs-lookup"><span data-stu-id="a9363-124">Usage</span></span>

1. <span data-ttu-id="a9363-125">将包引用添加到 [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)。</span><span class="sxs-lookup"><span data-stu-id="a9363-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="a9363-126">使用 `AddGrpcHttpApi` 在 *Startup.cs* 中注册服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="a9363-127">将 [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) 和 [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) 文件添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="a9363-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="a9363-128">用 HTTP 绑定和路由在 .proto 文件中注释 gRPC 方法：</span><span class="sxs-lookup"><span data-stu-id="a9363-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="a9363-129">`SayHello` gRPC 方法现在可以作为 gRPC+Protobuf 和 HTTP API 调用：</span><span class="sxs-lookup"><span data-stu-id="a9363-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="a9363-130">请求： `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="a9363-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="a9363-131">响应： `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="a9363-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="a9363-132">服务器日志显示 HTTP 调用是由 gRPC 服务执行的。</span><span class="sxs-lookup"><span data-stu-id="a9363-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="a9363-133">gRPC HTTP API 将传入的 HTTP 请求映射到 gRPC 消息，然后将响应消息转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="a9363-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="a9363-134">下面是一个基本示例。</span><span class="sxs-lookup"><span data-stu-id="a9363-134">This is a basic example.</span></span> <span data-ttu-id="a9363-135">如需了解更多自定义选项，请参见 [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)。</span><span class="sxs-lookup"><span data-stu-id="a9363-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="a9363-136">gRPC HTTP API 与 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="a9363-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="a9363-137">gRPC HTTP API 和 gRPC-Web 都支持从浏览器调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="a9363-138">但是，它们的操作方式是不同的：</span><span class="sxs-lookup"><span data-stu-id="a9363-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="a9363-139">gRPC-Web 允许浏览器应用通过 gRPC-Web 客户端和 Protobuf 从浏览器调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="a9363-140">gRPC-Web 需要浏览器应用生成 gRPC 客户端，并且具有快速发送小型 Protobuf 消息的优点。</span><span class="sxs-lookup"><span data-stu-id="a9363-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="a9363-141">gRPC HTTP API 允许浏览器应用调用 gRPC 服务，就像它们是使用 JSON 的 RESTful API 一样。</span><span class="sxs-lookup"><span data-stu-id="a9363-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="a9363-142">浏览器应用不需要生成 gRPC 客户端或了解 gRPC 的任何信息。</span><span class="sxs-lookup"><span data-stu-id="a9363-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="a9363-143">没有为 gRPC HTTP API 创建生成的客户端。</span><span class="sxs-lookup"><span data-stu-id="a9363-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="a9363-144">可以使用浏览器 JavaScript API 调用以前的 `Greeter` 服务：</span><span class="sxs-lookup"><span data-stu-id="a9363-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="a9363-145">试验性状态</span><span class="sxs-lookup"><span data-stu-id="a9363-145">Experimental status</span></span>

<span data-ttu-id="a9363-146">gRPC HTTP API 是一个试验。</span><span class="sxs-lookup"><span data-stu-id="a9363-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="a9363-147">它不完整，且不受支持。</span><span class="sxs-lookup"><span data-stu-id="a9363-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="a9363-148">我们对该技术及其支持应用开发者同时快速创建 gRPC 和 JSON 服务的功能感兴趣。</span><span class="sxs-lookup"><span data-stu-id="a9363-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="a9363-149">没有完成 gRPC HTTP API 的承诺。</span><span class="sxs-lookup"><span data-stu-id="a9363-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="a9363-150">我们想评估开发者对 gRPC HTTP API 的兴趣。</span><span class="sxs-lookup"><span data-stu-id="a9363-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="a9363-151">如果你对 gRPC HTTP API 感兴趣，请[提供反馈](https://github.com/grpc/grpc-dotnet/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="a9363-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="a9363-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="a9363-152">grpc-gateway</span></span>

<span data-ttu-id="a9363-153">[Grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) 是从 grpc 服务创建 RESTful JSON API 的另一种技术。</span><span class="sxs-lookup"><span data-stu-id="a9363-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="a9363-154">它使用相同的 .proto 注释将 HTTP 概念映射到 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="a9363-155">Grpc-gateway 和 gRPC HTTP API 最大的区别是 grpc-gateway 使用代码生成来创建反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="a9363-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="a9363-156">反向代理将 RESTful 调用转换为 gRPC，然后将它们发送到 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="a9363-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="a9363-157">如需了解 grpc-gateway 的安装和使用，请参阅 [grpc-gateway 文档](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html)。</span><span class="sxs-lookup"><span data-stu-id="a9363-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9363-158">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9363-158">Additional resources</span></span>

* [<span data-ttu-id="a9363-159">google.api.HttpRule 文档</span><span class="sxs-lookup"><span data-stu-id="a9363-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
