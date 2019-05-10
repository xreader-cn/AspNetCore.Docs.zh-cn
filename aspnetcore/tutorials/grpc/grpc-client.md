---
title: 教程：创建 .NET Core gRPC 客户端
author: juntaoluo
description: 此系列教程演示了如何在 ASP.NET Core 中创建gRPC 服务。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212649"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a>教程：创建 .NET Core gRPC 客户端

作者：[John Luo](https://github.com/juntaoluo)

本教程演示如何创建可与 gRPC 服务进行通信的 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端。

最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 客户端。
> * 针对上一个教程中创建的 gRPC Greeter 服务运行该服务。
> * 检查项目文件。

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a>创建 .NET 控制台应用程序

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

按照[此处](/dotnet/core/tutorials/with-visual-studio)的说明创建名为 GrpcGreeterClient 的控制台应用。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按照[此处](/dotnet/core/tutorials/with-visual-studio-code)的说明创建名为 GrpcGreeterClient 的控制台应用。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用。

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a>添加所需的包

将以下包添加到 gRPC 客户端项目：

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 包含适用于 C 核心客户端的 C# API。
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。 运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。

可以使用以下方法来添加包：

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a>用于安装包的 PMC 选项

* 从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”
* 从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录。
* 运行下面的命令：

    ```powershell
    Install-Package Grpc.Core
    ```

* 对 Google.Protobuf and Grpc.Tools 重复操作 `Install-Package`

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a>管理 NuGet 包选项以安装包

* 右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目
* 将“包源”设置为“nuget.org”
* 在搜索框中输入“Grpc.Core”
* 从“浏览”选项卡中选择“Grpc.Core”包，然后单击“安装”
* 为 Google.Protobuf 和 Grpc.Tools 重复上述操作

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

从“集成终端”中运行以下命令：

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

为 Google.Protobuf 和 Grpc.Tools 重复上述操作

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击“Solution Pad” > “添加包...”中的“包”文件夹
* 将“添加包”窗口的“源”下拉列表设置为“nuget.org”
* 在搜索框中输入“Grpc.Core”
* 从结果窗格中选择“Grpc.Core”包，然后单击“添加包”
* 为 Google.Protobuf 和 Grpc.Tools 重复上述操作

---

## <a name="add-the-greetproto-file"></a>添加 greet.proto 文件

从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目。 将 greet.proto 文件添加到 GrpcGreeterClient 项目文件的 `<Protobuf>` 物料组：

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> 可右键单击项目并从下拉菜单中选择“编辑 GrpcGreeterClient.csproj”选项来打开 GrpcGreeterClient 的项目文件。
>
> ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/edit_csproj.png)
>
> 此外，也可以导航到 GrpcGreeterClient 目录并使用喜欢的编辑器编辑 `GrpcGreeterClient.csproj`。

添加 `GrpcServices="Client"` 属性，以便仅为包含的 protobuf 文件生成 C# 客户端资产。 生成客户端项目以触发 C# 客户端资产的生成。

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a>创建 GreeterClient 并调用 SayHello 一元调用

将以下代码添加到 gRPC 客户端项目 `Program.cs` 文件的 `Main` 方法中：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

若要访问所需类型，需要使用以下 using 语句：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

GreeterClient 是通过实例化 `Channel` 创建的，后者包含用于创建与 gRPC 服务的连接的信息并用其来构造 `GreeterClient`：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

GreeterClient 包含可以异步调用的一元调用 `SayHello`：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

`SayHello` 调用的结果存储在 `reply` 中，之后可显示：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

当操作完成并发布所有资源时，应关闭客户端使用的 `Channel`：

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> 需要在 Greeter 命名空间中的类型可解析之前生成项目。 这些类型在项目生成期间自动生成，在项目生成过程运行之前不可用。

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>使用 gRPC Greeter 服务测试 gRPC 客户端

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 确保上一个教程中创建的 Greeter 服务正在运行。

* 服务运行后，返回到 GrpcGreeterClient 项目，将其设置为启动项目。 按 Ctrl+F5 以在不使用调试程序的情况下运行客户端。

  客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。 该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/client.png)

  该服务在写入命令提示符的日志中记录成功调用的详细信息。

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 确保上一个教程中创建的 Greeter 服务正在运行。

* 使用 `dotnet run` 从单独的命令行运行客户端项目 GrpcGreeter.Client。

客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。 该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

该服务在写入命令提示符的日志中记录成功调用的详细信息。

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
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>检查 gRPC 项目的项目文件

gRPC 客户端 GrpcGreeterClient 文件：

Program.cs 包含 gRPC 客户端的入口点和逻辑。

## <a name="additional-resources"></a>其他资源

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 gRPC 客户端。
> * 针对上一个教程中创建的 gRPC Greeter 服务运行该服务。
> * 检查项目文件。

> [!div class="step-by-step"]
> [上一篇：创建 gRPC Greeter 服务](xref:tutorials/grpc/grpc-start)
