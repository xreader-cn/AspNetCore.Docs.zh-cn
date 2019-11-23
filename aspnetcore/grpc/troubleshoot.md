---
title: 排查 .NET Core 上的 gRPC 问题
author: jamesnk
description: 排查在 .NET Core 上使用 gRPC 时遇到的错误。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519053"
---
# <a name="troubleshoot-grpc-on-net-core"></a>排查 .NET Core 上的 gRPC 问题

按[James 牛顿-k](https://twitter.com/jamesnk)

本文档讨论了在 .NET 上开发 gRPC 应用程序时经常遇到的问题。

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>客户端和服务 SSL/TLS 配置不匹配

默认情况下，gRPC 模板和示例使用[传输层安全性（TLS）](https://tools.ietf.org/html/rfc5246)来保护 gRPC 服务。 gRPC 客户端需要使用安全连接来成功调用受保护的 gRPC 服务。

可以在应用启动时，验证 ASP.NET Core gRPC 服务是否正在使用 TLS。 该服务将侦听 HTTPS 终结点：

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.NET Core 客户端必须使用服务器地址中 `https`，才能使用安全连接调用：

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

所有 gRPC 客户端实现都支持 TLS。 从其他语言 gRPC 的客户端通常需要配置有 `SslCredentials`的通道。 `SslCredentials` 指定客户端将使用的证书，并且必须使用该证书，而不是使用不安全凭据。 有关将不同 gRPC 客户端实现配置为使用 TLS 的示例，请参阅[GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>使用不受信任/无效证书调用 gRPC 服务

.NET gRPC 客户端要求服务具有可信证书。 在没有受信任的证书的情况下调用 gRPC 服务时，将返回以下错误消息：

> 未经处理的异常。 System.net.http.httprequestexception：无法建立 SSL 连接，请参阅内部异常。
> ---的 > AuthenticationException：根据验证过程，远程证书无效。

如果要在本地测试应用程序，并且不信任 ASP.NET Core HTTPS 开发证书，则可能会看到此错误。 有关解决此问题的说明，请参阅[在 Windows 和 macOS 上信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。

如果要在另一台计算机上调用 gRPC 服务，并且无法信任该证书，则可以将 gRPC 客户端配置为忽略无效的证书。 以下代码使用[HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback)来允许不带可信证书的调用：

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> 不受信任的证书只应在应用程序开发过程中使用。 生产应用应始终使用有效的证书。

## <a name="call-insecure-grpc-services-with-net-core-client"></a>通过 .NET Core 客户端调用不安全的 gRPC 服务

若要将不安全的 gRPC 服务与 .NET Core 客户端一起调用，需要进行其他配置。 GRPC 客户端必须将 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 开关设置为 `true` 并使用服务器地址中的 `http`：

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>无法在 macOS 上启动 ASP.NET Core gRPC 应用

Kestrel 不支持 macOS 上的 HTTP/2 和更早的 Windows 版本，如 Windows 7。 默认情况下，ASP.NET Core gRPC 模板和示例使用 TLS。 当您尝试启动 gRPC 服务器时，您将看到以下错误消息：

> 无法绑定到 IPv4 环回接口上的 https://localhost:5001：在 macOS 上不支持 HTTP/2，因为缺少 ALPN 支持。 "。

若要解决此问题，请将 Kestrel 和 gRPC 客户端配置为在不使用 TLS 的*情况下*使用 HTTP/2。 只应在开发过程中执行此操作。 如果不使用 TLS，将会在不加密的情况下发送 gRPC 消息。

Kestrel 必须在*Program.cs*中配置不包含 TLS 的 HTTP/2 终结点：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

如果未使用 TLS 配置 HTTP/2 终结点，则终结点的[ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols)必须设置为 `HttpProtocols.Http2`。 无法使用 `HttpProtocols.Http1AndHttp2`，因为需要使用 TLS 来协商 HTTP/2。 如果没有 TLS，与端点的所有连接默认为 HTTP/1.1，并且 gRPC 调用失败。

GRPC 客户端还必须配置为不使用 TLS。 有关详细信息，请参阅[Call 不安全的 gRPC services 与 .Net Core client](#call-insecure-grpc-services-with-net-core-client)。

> [!WARNING]
> 只应在应用程序开发过程中使用不带 TLS 的 HTTP/2。 生产应用应始终使用传输安全性。 有关详细信息，请参阅[gRPC for ASP.NET Core 中的安全注意事项](xref:grpc/security#transport-security)。

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gRPC C#资产不是从 proto 文件生成的代码

具体的客户端和服务基类的 gRPC 代码生成需要从项目引用 protobuf 文件和工具。 必须包括：

* 要在 `<Protobuf>` 项组中使用的*proto*文件。 [导入的*proto*文件](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)必须由项目引用。
* 对 gRPC 工具包[gRPC](https://www.nuget.org/packages/Grpc.Tools/)的引用。

有关生成 gRPC C#资产的详细信息，请参阅 <xref:grpc/basics>。

默认情况下，`<Protobuf>` 引用将生成具体的客户端和服务基类。 可以使用引用元素的 `GrpcServices` 特性来限制C#资产生成。 有效 `GrpcServices` 选项如下：

* `Both` （当不存在时则为默认值）
* `Server`
* `Client`
* `None`

托管 gRPC services 的 ASP.NET Core web 应用只需生成服务基类：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

发出 gRPC 调用的 gRPC 客户端应用只需要生成的具体客户端：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>WPF 项目无法从 proto 文件C#生成 gRPC 资产

WPF 项目有一个[已知问题](https://github.com/dotnet/wpf/issues/810)，可阻止 gRPC 代码生成正常运行。 在 WPF 项目中生成的任何 gRPC 类型（通过引用 `Grpc.Tools` 和*proto*文件）在使用时将创建编译错误：

> 错误 CS0246：找不到类型或命名空间名称 "MyGrpcServices" （是否缺少 using 指令或程序集引用？）

可以通过以下方式解决此问题：

1. 创建新的 .NET Core 类库项目。
2. 在新项目中，添加引用以启用[ C#从 *\*proto*文件生成的代码](xref:grpc/basics#generated-c-assets)：
    * 将包引用添加到 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包。
    * 将 *.proto\** 文件添加到 `<Protobuf>` 项目组。
3. 在 WPF 应用程序中，添加对新项目的引用。

WPF 应用程序可以使用来自新类库项目的 gRPC 生成类型。

[!INCLUDE[](~/includes/gRPCazure.md)]
