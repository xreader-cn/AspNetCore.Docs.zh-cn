---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 使用 ASP.NET Core 中编写 gRPC 服务时，请了解基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: ca06478e6168c59d9abf43d99213fa8a7091e178
ms.sourcegitcommit: 756114cab5e24e99ec23de62b0c1c16b15197ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67169521"
---
# <a name="grpc-services-with-aspnet-core"></a>使用 ASP.NET Core 的 gRPC 服务

本文档演示如何使用 ASP.NET Core gRPC 服务入门。

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>开始使用 ASP.NET Core 中的 gRPC 服务

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

请参阅[gRPC 服务入门](xref:tutorials/grpc/grpc-start)有关如何创建 gRPC 项目的详细说明。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

在命令行中运行 `dotnet new grpc -o GrpcGreeter`。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>将 gRPC 服务添加到 ASP.NET Core 应用

gRPC 需要以下程序包：

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)为 protobuf 消息 Api。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>配置 gRPC

使用启用 gRPC`AddGrpc`方法：

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

每个 gRPC 服务添加到路由通过管道`MapGrpcService`方法：

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core 中间件和功能共享路由管道，因此配置应用以提供额外的请求处理程序。 与配置的 gRPC 服务同时工作的其他请求处理程序，如 MVC 控制器。

## <a name="integration-with-aspnet-core-apis"></a>与 ASP.NET Core Api 集成

gRPC 服务具有完全访问权限的 ASP.NET Core 功能，如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)。 例如，服务实现可能会解决从通过构造函数的 DI 容器的记录器服务：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

默认情况下，gRPC 服务实现可以解析任何生存期 （单一实例、 作用域内或暂时） 与其他 DI 服务。

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC 方法中解决 HttpContext

GRPC API 提供了访问某些 HTTP/2 消息数据，如方法、 主机、 标头和尾部。 访问是通过`ServerCallContext`自变量传递给每个 gRPC 方法：

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` 不提供完全访问权限`HttpContext`所有 ASP.NET Api 中。 `GetHttpContext`扩展方法提供了全面的`HttpContext`表示 ASP.NET Api 中的基础 HTTP/2 消息：

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
