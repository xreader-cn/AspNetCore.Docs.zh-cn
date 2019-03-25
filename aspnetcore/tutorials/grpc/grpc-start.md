---
title: 教程：开始使用 ASP.NET Core 中的 gRPC
author: juntaoluo
description: 此系列教程演示了如何在 ASP.NET Core 中创建gRPC 服务。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 5f7a2f6b57804b3295b23c322dcbac553b05528b
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320051"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>教程：开始使用 ASP.NET Core 中的 gRPC 服务

作者：[John Luo](https://github.com/juntaoluo)

本教程介绍在 ASP.NET Core 上构建 gRPC 服务的基础知识。

在结束时，您将拥有回显问候语的 gRPC 服务。

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 服务。
> * 运行服务。
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

### <a name="test-the-service"></a>测试服务

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 请确保 GrpcGreeter.Server 已设为启动项目，然后按 Ctrl+F5 以在不使用调试器的情况下运行 gRPC 服务。

  Visual Studio 在命令提示符中运行该服务。 日志显示该服务已开始侦听 `http://localhost:50051`。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_start.png)

* 服务开始运行后，将 GrpcGreeter.Client 设为启动项目，然后按 Ctrl+F5 以在不使用调试器的情况下运行客户端。

  客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。 该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/client.png)

  该服务在写入命令提示符的日志中记录成功调用的详细信息。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 使用 `dotnet run` 从命令行运行服务器项目 GrpcGreeter.Server。 日志显示该服务已开始侦听 `http://localhost:50051`。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example\GrpcGreeter\GrpcGreeter.Server
```

* 使用 `dotnet run` 从单独的命令行运行客户端项目 GrpcGreeter.Client。

客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。 该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

该服务在写入命令提示符的日志中记录成功调用的详细信息。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\tp\GrpcGreeter\GrpcGreeter.Server
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 107.46730000000001ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>检查 gRPC 项目的项目文件

GrpcGreeter.Server 文件：

* greet.proto：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。 有关更多信息，请参见<xref:grpc/index>。
* Services 文件夹：包含 `Greeter` 服务的实现。
* appSettings.json：包含配置数据，如 Kestrel 使用的协议。 有关更多信息，请参见<xref:fundamentals/configuration/index>。
* Program.cs:包含 gRPC 服务的入口点。 有关更多信息，请参见<xref:fundamentals/host/web-host>。
* Startup.cs

包含配置应用行为的代码。 有关更多信息，请参见<xref:fundamentals/startup>。

gRPC 客户端 GrpcGreeter.Client 文件：

Program.cs 包含 gRPC 客户端的入口点和逻辑。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 服务。
> * 运行服务和客户端以对服务进行测试。
> * 检查项目文件。
