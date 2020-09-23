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
# <a name="test-services-with-grpc-tools"></a>用 gRPC 工具实现测试服务

作者：[James Newton-King](https://twitter.com/jamesnk)

gRPC 提供了工具，让开发人员可以在不构建客户端应用的情况下测试服务。 [gRPCurl](https://github.com/fullstorydev/grpcurl) 是一种命令行工具，可提供与 gRPC 服务的交互。 [gRPCui](https://github.com/fullstorydev/grpcui) 为 gRPC 添加交互式 Web UI。

本文介绍如何提供这种交互：

* 下载并安装 gRPCurl 和 gRPCui。
* 使用 gRPC ASP.NET Core 应用设置 gRPC 反射。
* 使用 `grpcurl` 发现和测试 gRPC 服务。
* 使用 `grpcui` 通过浏览器与 gRPC 服务交互。

## <a name="about-grpcurl"></a>关于 gRPCurl

gRPCurl 是由 gRPC 社区创建的命令行工具。 其功能包括：

* 调用 gRPC 服务，包括流式服务。
* 使用 [gRPC 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)进行服务发现。
* 列出并描述 gRPC 服务。
* 适用于安全 (TLS) 和不安全（纯文本）服务器。

有关下载和安装 `grpcurl` 的信息，请参阅 [gRPCurl GitHub 主页](https://github.com/fullstorydev/grpcurl#installation)。

## <a name="setup-grpc-reflection"></a>设置 gRPC 反射

`grpcurl` 需要了解服务的 Protobuf 协定，然后才能调用它们。 有两种方法可以实现此目的：

* 使用 gRPC 反射来发现服务协定。
* 在命令行参数中指定 .proto 文件。

将 gRPCurl 与 gRPC 反射和服务发现一起使用将更轻松。 gRPC ASP.NET Core 包含 [gRPC.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 包，因此具有对 gRPC 反射的内置支持。 在应用中配置反射：

* 添加 `Grpc.AspNetCore.Server.Reflection` 包引用。
* 在 Startup.cs 中注册反射：
  * `AddGrpcReflection` 用于注册启用反射的服务。
  * `MapGrpcReflectionService` 用于添加反射服务终结点。

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>使用 `grpcurl`

`-help` 参数说明 `grpcurl` 命令行选项：

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>发现服务

使用 `describe` 谓词来查看服务器定义的服务：

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

上面的示例：

* 在服务器 `localhost:5001` 上运行 `describe` 谓词。
* 打印 gRPC 反射返回的服务和方法。
  * `Greeter` 是应用实现的服务。
  * `ServerReflection` 是由`Grpc.AspNetCore.Server.Reflection` 包添加的服务。

将 `describe` 与服务、方法或消息名称组合以查看其详细信息：

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>调用 gRPC 服务

通过指定服务和方法名称以及表示请求消息的 JSON 参数来调用 gRPC 服务。 将 JSON 转换为 Protobuf 并发送到服务。

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

上面的示例：

* `-d` 参数使用 JSON 指定请求消息。 此参数必须位于服务器地址和方法名称之前。
* 调用 `greeter.Greeter` 服务上的 `SayHello` 方法。
* 以 JSON 的形式打印响应消息。

## <a name="about-grpcui"></a>关于 gRPCui

gRPCui 是 gRPC 的交互式 Web UI。 它基于 gRPCurl 构建，并提供一个 GUI 来发现和测试 gRPC 服务，类似于 Postman 等 HTTP 工具。

有关下载和安装 `grpcui` 的信息，请参阅 [gRPCui GitHub 主页](https://github.com/fullstorydev/grpcui#installation)。

## <a name="using-grpcui"></a>使用 `grpcui`

使用要与之交互的服务器地址作为参数运行 `grpcui`。

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

该工具将启动一个带有交互式 Web UI 的浏览器窗口。 使用 gRPC 反射自动发现 gRPC 服务。

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>其他资源

* [gRPCurl GitHub 主页](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub 主页](https://github.com/fullstorydev/grpcui)
* [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
