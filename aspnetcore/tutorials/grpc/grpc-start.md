---
title: 教程：开始使用 ASP.NET Core 中的 gRPC
author: juntaoluo
description: 此系列教程演示了如何在 ASP.NET Core 中创建gRPC 服务。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672562"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>教程：开始使用 ASP.NET Core 中的 gRPC 服务

作者：[John Luo](https://github.com/juntaoluo)

本教程介绍在 ASP.NET Core 上构建 gRPC 服务的基础知识。

在结束时，您将拥有回显问候语的 gRPC 服务。

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 服务。
> * 运行 gRPC 服务。
> * 检查项目文件。

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>创建 gRPC 服务

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。
* 创建新的 ASP.NET Core Web 应用呈现。
  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.1.png)
* 将项目命名为 GrpcGreeter。 将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配。
  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.2.png)
* 在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”。 选择“gRPC 服务”模板。

  创建以下初学者项目：

  ![“解决方案资源管理器”](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。
* 将目录更改为 (`cd`) 包含项目的文件夹。
* 运行以下命令：

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` 命令将在 GrpcGreeter 文件夹中创建一个新 gRPC 服务。
  * `code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹。

  一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。是否添加它们?”
* 选择“是”

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

从终端运行以下命令：

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。

### <a name="open-the-project"></a>打开项目

在 Visual Studio 中，选择“文件”>“打开”，然后选择“GrpcGreeter.sln”文件。

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a>运行服务

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 以在不使用调试程序的情况下运行 gRPC 服务。

  Visual Studio 在命令提示符中运行该服务。 日志显示该服务已开始侦听 `http://localhost:50051`。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter。 日志显示该服务已开始侦听 `http://localhost:50051`。

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
```

<!-- End of combined VS/Mac tabs -->

---

下一个教程将演示如何生成 gRPC 客户端，这是测试 Greeter 服务所必须执行的步骤。

### <a name="examine-the-project-files-of-the-grpc-project"></a>检查 gRPC 项目的项目文件

GrpcGreeter 文件：

* greet.proto：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。 有关更多信息，请参见<xref:grpc/index>。
* Services 文件夹：包含 `Greeter` 服务的实现。
* appSettings.json：包含配置数据，如 Kestrel 使用的协议。 有关更多信息，请参见<xref:fundamentals/configuration/index>。
* Program.cs:包含 gRPC 服务的入口点。 有关更多信息，请参见<xref:fundamentals/host/web-host>。
* Startup.cs：包含配置应用行为的代码。 有关更多信息，请参见<xref:fundamentals/startup>。

### <a name="test-the-service"></a>测试服务

## <a name="additional-resources"></a>其他资源

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 服务。
> * 运行 gRPC 服务。
> * 检查项目文件。

> [!div class="step-by-step"]
> [下一篇：创建 .NET Core gRPC 客户端](xref:tutorials/grpc/grpc-client)