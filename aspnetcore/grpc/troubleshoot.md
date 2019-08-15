---
title: 排查 .NET Core 上的 gRPC 问题
author: jamesnk
description: 排查在 .NET Core 上使用 gRPC 时遇到的错误。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029030"
---
# <a name="troubleshoot-grpc-on-net-core"></a>排查 .NET Core 上的 gRPC 问题

按[James 牛顿-k](https://twitter.com/jamesnk)

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>客户端和服务 SSL/TLS 配置不匹配

默认情况下, gRPC 模板和示例使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)来保护 gRPC 服务。 gRPC 客户端需要使用安全连接来成功调用受保护的 gRPC 服务。

可以在应用启动时, 验证 ASP.NET Core gRPC 服务是否正在使用 TLS。 该服务将侦听 HTTPS 终结点:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.Net Core 客户端必须在`https`服务器地址中使用才能使用安全连接调用:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

所有 gRPC 客户端实现都支持 TLS。 gRPC 从其他语言进行的客户端通常需要配置`SslCredentials`了的通道。 `SslCredentials`指定客户端将使用的证书, 并且必须使用该证书, 而不是使用不安全凭据。 有关将不同 gRPC 客户端实现配置为使用 TLS 的示例, 请参阅[GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。

## <a name="call-insecure-grpc-services-with-net-core-client"></a>通过 .NET Core 客户端调用不安全的 gRPC 服务

若要将不安全的 gRPC 服务与 .NET Core 客户端一起调用, 需要进行其他配置。 GRPC 客户端必须将`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`开关设置为`true` , 并在服务器地址中使用: `http`

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>无法在 macOS 上启动 ASP.NET Core gRPC 应用

Kestrel 不支持 macOS 上的 HTTP/2 和更早的 Windows 版本, 如 Windows 7。 默认情况下, ASP.NET Core gRPC 模板和示例使用 TLS。 当您尝试启动 gRPC 服务器时, 您将看到以下错误消息:

> 无法在 IPv4 环回 https://localhost:5001 接口上绑定到:由于缺少 ALPN 支持, macOS 上不支持 "HTTP/2 over TLS"。

若要解决此问题, 请将 Kestrel 和 gRPC 客户端配置为在不使用 TLS 的*情况下*使用 HTTP/2。 只应在开发过程中执行此操作。 如果不使用 TLS, 将会在不加密的情况下发送 gRPC 消息。

Kestrel 必须在*Program.cs*中配置不包含 TLS 的 HTTP/2 终结点:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

GRPC 客户端还必须配置为不使用 TLS。 有关详细信息, 请参阅[Call 不安全的 gRPC services 与 .Net Core client](#call-insecure-grpc-services-with-net-core-client)。

> [!WARNING]
> 只应在应用程序开发过程中使用不带 TLS 的 HTTP/2。 生产应用应始终使用传输安全性。 有关详细信息, 请参阅[gRPC for ASP.NET Core 中的安全注意事项](xref:grpc/security#transport-security)。

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gRPC C#资产不是从 *\*proto*文件生成的代码

具体的客户端和服务基类的 gRPC 代码生成需要从项目引用 protobuf 文件和工具。 必须包括:

* 要在`<Protobuf>`项组中使用的 proto 文件。 [导入的*proto*文件](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)必须由项目引用。
* 对 gRPC 工具包[gRPC](https://www.nuget.org/packages/Grpc.Tools/)的引用。

有关生成 gRPC C#资产的详细信息, 请<xref:grpc/basics>参阅。

默认情况下, `<Protobuf>`引用将生成具体的客户端和服务基类。 可以使用引用元素`GrpcServices`的特性来限制C#资产生成。 有效`GrpcServices`选项包括:

* `Both`(如果不存在则为默认值)
* `Server`
* `Client`
* `None`

托管 gRPC services 的 ASP.NET Core web 应用只需生成服务基类:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

发出 gRPC 调用的 gRPC 客户端应用只需要生成的具体客户端:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
