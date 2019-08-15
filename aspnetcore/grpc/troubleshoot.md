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
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="378c3-103">排查 .NET Core 上的 gRPC 问题</span><span class="sxs-lookup"><span data-stu-id="378c3-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="378c3-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="378c3-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="378c3-105">客户端和服务 SSL/TLS 配置不匹配</span><span class="sxs-lookup"><span data-stu-id="378c3-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="378c3-106">默认情况下, gRPC 模板和示例使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)来保护 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="378c3-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="378c3-107">gRPC 客户端需要使用安全连接来成功调用受保护的 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="378c3-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="378c3-108">可以在应用启动时, 验证 ASP.NET Core gRPC 服务是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="378c3-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="378c3-109">该服务将侦听 HTTPS 终结点:</span><span class="sxs-lookup"><span data-stu-id="378c3-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="378c3-110">.Net Core 客户端必须在`https`服务器地址中使用才能使用安全连接调用:</span><span class="sxs-lookup"><span data-stu-id="378c3-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="378c3-111">所有 gRPC 客户端实现都支持 TLS。</span><span class="sxs-lookup"><span data-stu-id="378c3-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="378c3-112">gRPC 从其他语言进行的客户端通常需要配置`SslCredentials`了的通道。</span><span class="sxs-lookup"><span data-stu-id="378c3-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="378c3-113">`SslCredentials`指定客户端将使用的证书, 并且必须使用该证书, 而不是使用不安全凭据。</span><span class="sxs-lookup"><span data-stu-id="378c3-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="378c3-114">有关将不同 gRPC 客户端实现配置为使用 TLS 的示例, 请参阅[GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。</span><span class="sxs-lookup"><span data-stu-id="378c3-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="378c3-115">通过 .NET Core 客户端调用不安全的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="378c3-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="378c3-116">若要将不安全的 gRPC 服务与 .NET Core 客户端一起调用, 需要进行其他配置。</span><span class="sxs-lookup"><span data-stu-id="378c3-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="378c3-117">GRPC 客户端必须将`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`开关设置为`true` , 并在服务器地址中使用: `http`</span><span class="sxs-lookup"><span data-stu-id="378c3-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="378c3-118">无法在 macOS 上启动 ASP.NET Core gRPC 应用</span><span class="sxs-lookup"><span data-stu-id="378c3-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="378c3-119">Kestrel 不支持 macOS 上的 HTTP/2 和更早的 Windows 版本, 如 Windows 7。</span><span class="sxs-lookup"><span data-stu-id="378c3-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="378c3-120">默认情况下, ASP.NET Core gRPC 模板和示例使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="378c3-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="378c3-121">当您尝试启动 gRPC 服务器时, 您将看到以下错误消息:</span><span class="sxs-lookup"><span data-stu-id="378c3-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="378c3-122">无法在 IPv4 环回 https://localhost:5001 接口上绑定到:由于缺少 ALPN 支持, macOS 上不支持 "HTTP/2 over TLS"。</span><span class="sxs-lookup"><span data-stu-id="378c3-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="378c3-123">若要解决此问题, 请将 Kestrel 和 gRPC 客户端配置为在不使用 TLS 的*情况下*使用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="378c3-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="378c3-124">只应在开发过程中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="378c3-124">You should only do this during development.</span></span> <span data-ttu-id="378c3-125">如果不使用 TLS, 将会在不加密的情况下发送 gRPC 消息。</span><span class="sxs-lookup"><span data-stu-id="378c3-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="378c3-126">Kestrel 必须在*Program.cs*中配置不包含 TLS 的 HTTP/2 终结点:</span><span class="sxs-lookup"><span data-stu-id="378c3-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="378c3-127">GRPC 客户端还必须配置为不使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="378c3-127">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="378c3-128">有关详细信息, 请参阅[Call 不安全的 gRPC services 与 .Net Core client](#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="378c3-128">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="378c3-129">只应在应用程序开发过程中使用不带 TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="378c3-129">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="378c3-130">生产应用应始终使用传输安全性。</span><span class="sxs-lookup"><span data-stu-id="378c3-130">Production apps should always use transport security.</span></span> <span data-ttu-id="378c3-131">有关详细信息, 请参阅[gRPC for ASP.NET Core 中的安全注意事项](xref:grpc/security#transport-security)。</span><span class="sxs-lookup"><span data-stu-id="378c3-131">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="378c3-132">gRPC C#资产不是从 *\*proto*文件生成的代码</span><span class="sxs-lookup"><span data-stu-id="378c3-132">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="378c3-133">具体的客户端和服务基类的 gRPC 代码生成需要从项目引用 protobuf 文件和工具。</span><span class="sxs-lookup"><span data-stu-id="378c3-133">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="378c3-134">必须包括:</span><span class="sxs-lookup"><span data-stu-id="378c3-134">You must include:</span></span>

* <span data-ttu-id="378c3-135">要在`<Protobuf>`项组中使用的 proto 文件。</span><span class="sxs-lookup"><span data-stu-id="378c3-135">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="378c3-136">[导入的*proto*文件](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)必须由项目引用。</span><span class="sxs-lookup"><span data-stu-id="378c3-136">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="378c3-137">对 gRPC 工具包[gRPC](https://www.nuget.org/packages/Grpc.Tools/)的引用。</span><span class="sxs-lookup"><span data-stu-id="378c3-137">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="378c3-138">有关生成 gRPC C#资产的详细信息, 请<xref:grpc/basics>参阅。</span><span class="sxs-lookup"><span data-stu-id="378c3-138">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="378c3-139">默认情况下, `<Protobuf>`引用将生成具体的客户端和服务基类。</span><span class="sxs-lookup"><span data-stu-id="378c3-139">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="378c3-140">可以使用引用元素`GrpcServices`的特性来限制C#资产生成。</span><span class="sxs-lookup"><span data-stu-id="378c3-140">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="378c3-141">有效`GrpcServices`选项包括:</span><span class="sxs-lookup"><span data-stu-id="378c3-141">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="378c3-142">`Both`(如果不存在则为默认值)</span><span class="sxs-lookup"><span data-stu-id="378c3-142">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="378c3-143">托管 gRPC services 的 ASP.NET Core web 应用只需生成服务基类:</span><span class="sxs-lookup"><span data-stu-id="378c3-143">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="378c3-144">发出 gRPC 调用的 gRPC 客户端应用只需要生成的具体客户端:</span><span class="sxs-lookup"><span data-stu-id="378c3-144">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
