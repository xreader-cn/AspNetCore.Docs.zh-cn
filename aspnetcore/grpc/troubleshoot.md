---
title: 排查 .NET Core 上的 gRPC 问题
author: jamesnk
description: 排查在 .NET Core 上使用 gRPC 时遇到的错误。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/26/2019
uid: grpc/troubleshoot
ms.openlocfilehash: e0c12aac083bc2e13f66831e756f2a93b7ee76b0
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310449"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="20441-103">排查 .NET Core 上的 gRPC 问题</span><span class="sxs-lookup"><span data-stu-id="20441-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="20441-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="20441-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="20441-105">本文档讨论了在 .NET 上开发 gRPC 应用程序时经常遇到的问题。</span><span class="sxs-lookup"><span data-stu-id="20441-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="20441-106">客户端和服务 SSL/TLS 配置不匹配</span><span class="sxs-lookup"><span data-stu-id="20441-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="20441-107">默认情况下，gRPC 模板和示例使用[传输层安全性（TLS）](https://tools.ietf.org/html/rfc5246)来保护 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="20441-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="20441-108">gRPC 客户端需要使用安全连接来成功调用受保护的 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="20441-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="20441-109">可以在应用启动时，验证 ASP.NET Core gRPC 服务是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="20441-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="20441-110">该服务将侦听 HTTPS 终结点：</span><span class="sxs-lookup"><span data-stu-id="20441-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="20441-111">.Net Core 客户端必须在`https`服务器地址中使用才能使用安全连接调用：</span><span class="sxs-lookup"><span data-stu-id="20441-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="20441-112">所有 gRPC 客户端实现都支持 TLS。</span><span class="sxs-lookup"><span data-stu-id="20441-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="20441-113">gRPC 从其他语言进行的客户端通常需要配置`SslCredentials`了的通道。</span><span class="sxs-lookup"><span data-stu-id="20441-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="20441-114">`SslCredentials`指定客户端将使用的证书，并且必须使用该证书，而不是使用不安全凭据。</span><span class="sxs-lookup"><span data-stu-id="20441-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="20441-115">有关将不同 gRPC 客户端实现配置为使用 TLS 的示例，请参阅[GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。</span><span class="sxs-lookup"><span data-stu-id="20441-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="20441-116">使用不受信任/无效证书调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="20441-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="20441-117">.NET gRPC 客户端要求服务具有可信证书。</span><span class="sxs-lookup"><span data-stu-id="20441-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="20441-118">在没有受信任的证书的情况下调用 gRPC 服务时，将返回以下错误消息：</span><span class="sxs-lookup"><span data-stu-id="20441-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="20441-119">未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="20441-119">Unhandled exception.</span></span> <span data-ttu-id="20441-120">系统 System.net.http.httprequestexception：无法建立 SSL 连接，请参阅内部异常。</span><span class="sxs-lookup"><span data-stu-id="20441-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="20441-121">---> AuthenticationException：根据验证过程，远程证书无效。</span><span class="sxs-lookup"><span data-stu-id="20441-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="20441-122">如果要在本地测试应用程序，并且不信任 ASP.NET Core HTTPS 开发证书，则可能会看到此错误。</span><span class="sxs-lookup"><span data-stu-id="20441-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="20441-123">有关解决此问题的说明，请参阅[在 Windows 和 macOS 上信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="20441-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="20441-124">如果要在另一台计算机上调用 gRPC 服务，并且无法信任该证书，则可以将 gRPC 客户端配置为忽略无效的证书。</span><span class="sxs-lookup"><span data-stu-id="20441-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="20441-125">以下代码使用[HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback)来允许不带可信证书的调用：</span><span class="sxs-lookup"><span data-stu-id="20441-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;

var httpClient = new HttpClient(httpClientHandler);
httpClient.BaseAddress = new Uri("https://localhost:5001");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="20441-126">不受信任的证书只应在应用程序开发过程中使用。</span><span class="sxs-lookup"><span data-stu-id="20441-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="20441-127">生产应用应始终使用有效的证书。</span><span class="sxs-lookup"><span data-stu-id="20441-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="20441-128">通过 .NET Core 客户端调用不安全的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="20441-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="20441-129">若要将不安全的 gRPC 服务与 .NET Core 客户端一起调用，需要进行其他配置。</span><span class="sxs-lookup"><span data-stu-id="20441-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="20441-130">GRPC 客户端必须将`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`开关设置为`true` ，并在服务器地址中使用： `http`</span><span class="sxs-lookup"><span data-stu-id="20441-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="20441-131">无法在 macOS 上启动 ASP.NET Core gRPC 应用</span><span class="sxs-lookup"><span data-stu-id="20441-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="20441-132">Kestrel 不支持 macOS 上的 HTTP/2 和更早的 Windows 版本，如 Windows 7。</span><span class="sxs-lookup"><span data-stu-id="20441-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="20441-133">默认情况下，ASP.NET Core gRPC 模板和示例使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="20441-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="20441-134">当您尝试启动 gRPC 服务器时，您将看到以下错误消息：</span><span class="sxs-lookup"><span data-stu-id="20441-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="20441-135">无法在 IPv4 环回 https://localhost:5001 接口上绑定到：由于缺少 ALPN 支持，macOS 上不支持 "HTTP/2 over TLS"。</span><span class="sxs-lookup"><span data-stu-id="20441-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="20441-136">若要解决此问题，请将 Kestrel 和 gRPC 客户端配置为在不使用 TLS 的*情况下*使用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="20441-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="20441-137">只应在开发过程中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="20441-137">You should only do this during development.</span></span> <span data-ttu-id="20441-138">如果不使用 TLS，将会在不加密的情况下发送 gRPC 消息。</span><span class="sxs-lookup"><span data-stu-id="20441-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="20441-139">Kestrel 必须在*Program.cs*中配置不包含 TLS 的 HTTP/2 终结点：</span><span class="sxs-lookup"><span data-stu-id="20441-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="20441-140">如果未使用 TLS 配置 HTTP/2 终结点，则终结点的[ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols)必须设置为`HttpProtocols.Http2`。</span><span class="sxs-lookup"><span data-stu-id="20441-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="20441-141">`HttpProtocols.Http1AndHttp2`无法使用，因为需要使用 TLS 来协商 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="20441-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="20441-142">如果没有 TLS，与端点的所有连接默认为 HTTP/1.1，并且 gRPC 调用失败。</span><span class="sxs-lookup"><span data-stu-id="20441-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="20441-143">GRPC 客户端还必须配置为不使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="20441-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="20441-144">有关详细信息，请参阅[Call 不安全的 gRPC services 与 .Net Core client](#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="20441-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="20441-145">只应在应用程序开发过程中使用不带 TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="20441-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="20441-146">生产应用应始终使用传输安全性。</span><span class="sxs-lookup"><span data-stu-id="20441-146">Production apps should always use transport security.</span></span> <span data-ttu-id="20441-147">有关详细信息，请参阅[gRPC for ASP.NET Core 中的安全注意事项](xref:grpc/security#transport-security)。</span><span class="sxs-lookup"><span data-stu-id="20441-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="20441-148">gRPC C#资产不是从 *\*proto*文件生成的代码</span><span class="sxs-lookup"><span data-stu-id="20441-148">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="20441-149">具体的客户端和服务基类的 gRPC 代码生成需要从项目引用 protobuf 文件和工具。</span><span class="sxs-lookup"><span data-stu-id="20441-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="20441-150">必须包括：</span><span class="sxs-lookup"><span data-stu-id="20441-150">You must include:</span></span>

* <span data-ttu-id="20441-151">要在`<Protobuf>`项组中使用的 proto 文件。</span><span class="sxs-lookup"><span data-stu-id="20441-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="20441-152">[导入的*proto*文件](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)必须由项目引用。</span><span class="sxs-lookup"><span data-stu-id="20441-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="20441-153">对 gRPC 工具包[gRPC](https://www.nuget.org/packages/Grpc.Tools/)的引用。</span><span class="sxs-lookup"><span data-stu-id="20441-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="20441-154">有关生成 gRPC C#资产的详细信息，请<xref:grpc/basics>参阅。</span><span class="sxs-lookup"><span data-stu-id="20441-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="20441-155">默认情况下， `<Protobuf>`引用将生成具体的客户端和服务基类。</span><span class="sxs-lookup"><span data-stu-id="20441-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="20441-156">可以使用引用元素`GrpcServices`的特性来限制C#资产生成。</span><span class="sxs-lookup"><span data-stu-id="20441-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="20441-157">有效`GrpcServices`选项包括：</span><span class="sxs-lookup"><span data-stu-id="20441-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="20441-158">`Both`（如果不存在则为默认值）</span><span class="sxs-lookup"><span data-stu-id="20441-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="20441-159">托管 gRPC services 的 ASP.NET Core web 应用只需生成服务基类：</span><span class="sxs-lookup"><span data-stu-id="20441-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="20441-160">发出 gRPC 调用的 gRPC 客户端应用只需要生成的具体客户端：</span><span class="sxs-lookup"><span data-stu-id="20441-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
