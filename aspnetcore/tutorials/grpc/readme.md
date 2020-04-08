---
page_type: sample
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648156"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>使用 Visual Studio 在 ASP.NET Core 3.0 中创建 gRPC 客户端和服务器

本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。

最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。

本教程介绍以下操作：

* 创建 gRPC 服务器。
* 创建 gRPC 客户端。
* 使用 gRPC Greeter 服务测试 gRPC 客户端服务。

## <a name="create-a-grpc-service"></a>创建 gRPC 服务

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。
* 在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。
* 选择“下一步” 
* 将项目命名为 GrpcGreeter  。 将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。
* 选择“创建” 
* 在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：
  * 在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。 
  * 选择“gRPC 服务”模板  。
  * 选择“创建” 

### <a name="run-the-service"></a>运行服务

* 按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。

  Visual Studio 在命令提示符中运行该服务。

日志显示该服务正在侦听 `https://localhost:5001`。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> gRPC 模板配置为使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。 gRPC 客户端需要使用 HTTPS 调用服务器。
>
> macOS 不支持 ASP.NET Core gRPC 及 TLS。 在 macOS 上成功运行 gRPC 服务需要其他配置。 有关详细信息，请参阅 [macOS 上的 gRPC 和 ASP.NET Core](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos)。

### <a name="examine-the-project-files"></a>检查项目文件

GrpcGreeter 项目文件  ：

* *greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。 有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。
* Services  文件夹：包含 `Greeter` 服务的实现。
* *appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。 有关详细信息，请参阅 <xref:fundamentals/configuration/index>。
* Program.cs  :包含 gRPC 服务的入口点。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。
* *Startup.cs*：包含配置应用行为的代码。 有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。

## <a name="create-the-grpc-client-in-a-net-console-app"></a>在 .NET 控制台应用中创建 gRPC 客户端

* 打开 Visual Studio 的第二个实例。
* 从菜单栏中选择“文件”   > “新建”   > “项目”  。
* 在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。
* 选择“下一步” 
* 在“名称”文本框中，输入“GrpcGreeterClient”  。
* 选择“创建”  。

### <a name="add-required-packages"></a>添加所需的包

gRPC 客户端项目需要以下包：

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。 运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。

通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。

#### <a name="pmc-option-to-install-packages"></a>用于安装包的 PMC 选项

* 从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   
* 从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。
* 运行以下命令：

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>管理 NuGet 包选项以安装包

* 右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  
* 选择“浏览”选项卡  。
* 在搜索框中输入 Grpc.Net.Client  。
* 从“浏览”选项卡中选择“Grpc.Net.Client”包，然后选择“安装”    。
* 为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。

### <a name="add-greetproto"></a>添加 greet.proto

* 在 gRPC 客户端项目中创建 Protos 文件夹  。
* 从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。
* 编辑 GrpcGreeterClient.csproj 项目文件  ：

  右键单击项目，并选择“编辑项目文件”  。

* 添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>创建 Greeter 客户端

构建项目，以在 `GrpcGreeter` 命名空间中创建类型。 `GrpcGreeter` 类型是由生成进程自动生成的。

使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

Program.cs  包含 gRPC 客户端的入口点和逻辑。

通过以下方式创建 Greeter 客户端：

* 实例化 `GrpcChannel`，使其包含用于创建到 gRPC 服务的连接的信息。
* 使用 `GrpcChannel` 构造 Greeter 客户端。

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>使用 gRPC Greeter 服务测试 gRPC 客户端

* 在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。
* 在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动客户端。

客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。 该服务会发送“Hello GreeterClient”消息作为答复。 “Hello GreeterClient”答复将在命令提示符中显示：

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>文档帮助和 gRPC 的后续步骤

* [ASP.NET Core 上 gRPC 的简介](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [使用 C# 的 gRPC 服务](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [将 gRPC 服务从 C-core 迁移到 ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
