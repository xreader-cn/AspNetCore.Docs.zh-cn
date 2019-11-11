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
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463048"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="827a7-102">使用 Visual Studio 在 ASP.NET Core 3.0 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="827a7-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="827a7-103">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="827a7-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="827a7-104">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="827a7-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="827a7-105">本教程介绍以下操作：</span><span class="sxs-lookup"><span data-stu-id="827a7-105">In this tutorial, you;</span></span>

* <span data-ttu-id="827a7-106">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="827a7-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="827a7-107">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="827a7-107">Create a gRPC client.</span></span>
* <span data-ttu-id="827a7-108">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="827a7-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="827a7-109">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="827a7-109">Create a gRPC service</span></span>

* <span data-ttu-id="827a7-110">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="827a7-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="827a7-111">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="827a7-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="827a7-112">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="827a7-112">Select **Next**</span></span>
* <span data-ttu-id="827a7-113">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="827a7-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="827a7-114">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="827a7-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="827a7-115">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="827a7-115">Select **Create**</span></span>
* <span data-ttu-id="827a7-116">在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="827a7-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="827a7-117">在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="827a7-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="827a7-118">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="827a7-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="827a7-119">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="827a7-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="827a7-120">运行该服务</span><span class="sxs-lookup"><span data-stu-id="827a7-120">Run the service</span></span>

* <span data-ttu-id="827a7-121">按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="827a7-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="827a7-122">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="827a7-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="827a7-123">日志显示该服务正在侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="827a7-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="827a7-124">gRPC 模板配置为使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。</span><span class="sxs-lookup"><span data-stu-id="827a7-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="827a7-125">gRPC 客户端需要使用 HTTPS 调用服务器。</span><span class="sxs-lookup"><span data-stu-id="827a7-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="827a7-126">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="827a7-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="827a7-127">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="827a7-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="827a7-128">有关详细信息，请参阅 [macOS 上的 gRPC 和 ASP.NET Core](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="827a7-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="827a7-129">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="827a7-129">Examine the project files</span></span>

<span data-ttu-id="827a7-130">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="827a7-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="827a7-131">*greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="827a7-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="827a7-132">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="827a7-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="827a7-133">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="827a7-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="827a7-134">*appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="827a7-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="827a7-135">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="827a7-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="827a7-136">Program.cs  ：包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="827a7-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="827a7-137">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="827a7-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="827a7-138">*Startup.cs*：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="827a7-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="827a7-139">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="827a7-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="827a7-140">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="827a7-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="827a7-141">打开 Visual Studio 的第二个实例。</span><span class="sxs-lookup"><span data-stu-id="827a7-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="827a7-142">从菜单栏中选择“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="827a7-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="827a7-143">在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。</span><span class="sxs-lookup"><span data-stu-id="827a7-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="827a7-144">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="827a7-144">Select **Next**</span></span>
* <span data-ttu-id="827a7-145">在“名称”文本框中，输入“GrpcGreeterClient”  。</span><span class="sxs-lookup"><span data-stu-id="827a7-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="827a7-146">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="827a7-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="827a7-147">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="827a7-147">Add required packages</span></span>

<span data-ttu-id="827a7-148">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="827a7-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="827a7-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="827a7-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="827a7-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="827a7-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="827a7-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="827a7-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="827a7-152">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="827a7-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="827a7-153">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="827a7-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="827a7-154">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="827a7-154">PMC option to install packages</span></span>

* <span data-ttu-id="827a7-155">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="827a7-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="827a7-156">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。</span><span class="sxs-lookup"><span data-stu-id="827a7-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="827a7-157">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="827a7-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="827a7-158">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="827a7-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="827a7-159">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="827a7-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="827a7-160">选择“浏览”按钮  。</span><span class="sxs-lookup"><span data-stu-id="827a7-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="827a7-161">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="827a7-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="827a7-162">从“浏览”选项卡中选择“Grpc.Net.Client”包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="827a7-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="827a7-163">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="827a7-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="827a7-164">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="827a7-164">Add greet.proto</span></span>

* <span data-ttu-id="827a7-165">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="827a7-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="827a7-166">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="827a7-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="827a7-167">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="827a7-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="827a7-168">右键单击项目，并选择“编辑项目文件”  。</span><span class="sxs-lookup"><span data-stu-id="827a7-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="827a7-169">添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="827a7-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="827a7-170">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="827a7-170">Create the Greeter client</span></span>

<span data-ttu-id="827a7-171">构建项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="827a7-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="827a7-172">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="827a7-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="827a7-173">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="827a7-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="827a7-174">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="827a7-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="827a7-175">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="827a7-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="827a7-176">实例化 `GrpcChannel`，使其包含用于创建到 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="827a7-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="827a7-177">使用 `GrpcChannel` 构造 Greeter 客户端。</span><span class="sxs-lookup"><span data-stu-id="827a7-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="827a7-178">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="827a7-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="827a7-179">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="827a7-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="827a7-180">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动客户端。</span><span class="sxs-lookup"><span data-stu-id="827a7-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="827a7-181">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="827a7-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="827a7-182">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="827a7-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="827a7-183">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="827a7-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="827a7-184">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="827a7-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="827a7-185">文档帮助和 gRPC 的后续步骤</span><span class="sxs-lookup"><span data-stu-id="827a7-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="827a7-186">ASP.NET Core 上 gRPC 的简介</span><span class="sxs-lookup"><span data-stu-id="827a7-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="827a7-187">使用 C# 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="827a7-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="827a7-188">将 gRPC 服务从 C-core 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="827a7-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
