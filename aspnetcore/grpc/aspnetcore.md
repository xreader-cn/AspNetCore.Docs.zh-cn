---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解 ASP.NET Core 编写 gRPC services 时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 26f0d7610151460967b97665ed61deab1ef56d68
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862930"
---
# <a name="grpc-services-with-aspnet-core"></a>使用 ASP.NET Core 的 gRPC 服务

本文档演示如何使用 ASP.NET Core 开始使用 gRPC 服务。

## <a name="prerequisites"></a>系统必备

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>开始使用 ASP.NET Core 中的 gRPC 服务

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

有关如何创建 gRPC 项目的详细说明, 请参阅[gRPC services 入门](xref:tutorials/grpc/grpc-start)。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

在命令行中运行 `dotnet new grpc -o GrpcGreeter`。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>将 gRPC 服务添加到 ASP.NET Core 应用

gRPC 需要[gRPC](https://www.nuget.org/packages/Grpc.AspNetCore)包。

### <a name="configure-grpc"></a>配置 gRPC

gRPC 是通过`AddGrpc`方法启用的:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

每个 gRPC 服务通过`MapGrpcService`方法添加到路由管道:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core 中间件和功能共享路由管道, 因此可以将应用配置为提供其他请求处理程序。 其他请求处理程序 (如 MVC 控制器) 与已配置的 gRPC 服务并行工作。

## <a name="integration-with-aspnet-core-apis"></a>与 ASP.NET Core Api 集成

gRPC 服务对 ASP.NET Core 功能 (如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)) 具有完全访问权限。 例如, 服务实现可以通过构造函数从 DI 容器解析记录器服务:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

默认情况下, gRPC 服务实现可以解析具有任意生存期 (单独、作用域或暂时性) 的其他 DI 服务。

### <a name="resolve-httpcontext-in-grpc-methods"></a>解析 gRPC 方法中的 HttpContext

GRPC API 提供对某些 HTTP/2 消息数据 (如方法、主机、标头和尾部) 的访问权限。 通过传递给每`ServerCallContext`个 gRPC 方法的参数访问:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`在所有 ASP.NET api 中都`HttpContext`不提供对的完全访问权限。 扩展方法提供对在 ASP.NET api 中`HttpContext`表示基础 HTTP/2 消息的完全访问权限: `GetHttpContext`

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="grpc-and-aspnet-core-on-macos"></a>macOS 上的 gRPC 和 ASP.NET Core

Kestrel 不支持 macOS 上的[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)的 HTTP/2。 默认情况下, ASP.NET Core gRPC 模板和示例使用 TLS。 当您尝试启动 gRPC 服务器时, 您将看到以下错误消息:

> 无法在 IPv4 环回 https://localhost:5001 接口上绑定到:由于缺少 ALPN 支持, macOS 上不支持 "HTTP/2 over TLS"。

若要解决此问题, 请将 Kestrel 和 gRPC 客户端配置为在不使用 TLS 的**情况下**使用 HTTP/2。 只应在开发过程中执行此操作。 如果不使用 TLS, 将会在不加密的情况下发送 gRPC 消息。

Kestrel 必须在中`Program.cs`配置不包含 TLS 的 HTTP/2 终结点:

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

GRPC 客户端必须将`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`开关设置为`true` , 并在服务器地址中使用: `http`

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> 只应在应用程序开发过程中使用不带 TLS 的 HTTP/2。 生产应用程序应始终使用传输安全性。 有关详细信息, 请参阅[gRPC for ASP.NET Core 中的安全注意事项](xref:grpc/security#transport-security)。

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
