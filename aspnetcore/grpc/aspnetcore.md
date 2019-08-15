---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解 ASP.NET Core 编写 gRPC services 时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 38111c152c581c50767f9cd4e5fa257bd3fd804e
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022314"
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

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
