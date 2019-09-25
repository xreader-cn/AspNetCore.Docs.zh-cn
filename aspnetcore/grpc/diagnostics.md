---
title: .NET 上的 gRPC 中的日志记录和诊断
author: jamesnk
description: 了解如何从 .NET 上的 gRPC 应用程序收集诊断信息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250738"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET 上的 gRPC 中的日志记录和诊断

按[James 牛顿-k](https://twitter.com/jamesnk)

本文介绍如何从 gRPC 应用程序收集诊断信息，以帮助解决问题。

## <a name="grpc-services-logging"></a>gRPC services 日志记录

> [!WARNING]
> 服务器端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

由于 gRPC 服务托管在 ASP.NET Core 上，因此它使用 ASP.NET Core 日志记录系统。 在默认配置中，gRPC 记录的信息非常小，但这可以进行配置。 有关配置 ASP.NET Core 日志记录的详细信息，请参阅有关[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)的文档。

gRPC 会将日志添加`Grpc`到类别下。 若要从 gRPC 启用详细日志，请`Grpc`通过将以下`Debug`项添加到中`LogLevel` `Logging`的子部分，将前缀配置为 appsettings 文件中的级别：

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

你还可以在*Startup.cs*中通过以下`ConfigureLogging`方式配置此项：

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：

* `Logging:LogLevel:Grpc` = `Debug`

查看配置系统的文档以确定如何指定嵌套配置值。 例如，使用环境变量时，将使用`_`两个字符，而不`:`是（例如`Logging__LogLevel__Grpc`）。

建议为应用收集`Debug`更详细的诊断时使用级别。 此`Trace`级别生成非常低级别的诊断，很少需要诊断应用中的问题。

### <a name="sample-logging-output"></a>日志记录输出示例

下面是 gRPC 服务`Debug`级别的控制台输出示例：

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

## <a name="access-server-side-logs"></a>访问服务器端日志

访问服务器端日志的方式取决于运行的环境。

### <a name="as-a-console-app"></a>作为控制台应用

如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console-provider)。 gRPC 日志将显示在控制台中。

### <a name="other-environments"></a>其他环境

如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅<xref:fundamentals/logging/index> ，以了解有关如何配置适用于环境的日志记录提供程序的详细信息。

## <a name="grpc-client-logging"></a>gRPC 客户端日志记录

> [!WARNING]
> 客户端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

若要从 .net 客户端获取日志，可以在创建`GrpcChannelOptions.LoggerFactory`客户端通道时设置属性。 如果要从 ASP.NET Core 的应用程序调用 gRPC 服务，则可以通过依赖关系注入（DI）来解析记录器工厂：

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

启用客户端日志记录的另一种方法是使用[gRPC 客户端工厂](xref:grpc/clientfactory)来创建客户端。 向客户端工厂注册并从 DI 解析的 gRPC 客户端将自动使用应用的配置日志记录。

如果你的应用未使用 DI，则可以使用`ILoggerFactory` [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)创建新的实例。 若要访问此方法，请向应用程序中[添加 ""。](https://www.nuget.org/packages/microsoft.extensions.logging/)

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a>日志记录输出示例

下面是 gRPC 客户端`Debug`级别的控制台输出示例：

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
