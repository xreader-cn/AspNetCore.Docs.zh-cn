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
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="9f250-104">教程：创建 .NET Core gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="9f250-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="9f250-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="9f250-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="9f250-106">本教程演示如何创建可与 gRPC 服务进行通信的 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端。</span><span class="sxs-lookup"><span data-stu-id="9f250-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="9f250-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="9f250-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="9f250-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9f250-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f250-109">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="9f250-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="9f250-110">针对上一个教程中创建的 gRPC Greeter 服务运行该服务。</span><span class="sxs-lookup"><span data-stu-id="9f250-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="9f250-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="9f250-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="9f250-112">创建 .NET 控制台应用程序</span><span class="sxs-lookup"><span data-stu-id="9f250-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f250-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f250-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9f250-114">按照[此处](/dotnet/core/tutorials/with-visual-studio)的说明创建名为 GrpcGreeterClient 的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="9f250-114">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9f250-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f250-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9f250-116">按照[此处](/dotnet/core/tutorials/with-visual-studio-code)的说明创建名为 GrpcGreeterClient 的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="9f250-116">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9f250-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9f250-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9f250-118">按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="9f250-118">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="9f250-119">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="9f250-119">Add required packages</span></span>

<span data-ttu-id="9f250-120">将以下包添加到 gRPC 客户端项目：</span><span class="sxs-lookup"><span data-stu-id="9f250-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="9f250-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 包含适用于 C 核心客户端的 C# API。</span><span class="sxs-lookup"><span data-stu-id="9f250-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="9f250-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="9f250-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9f250-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="9f250-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9f250-124">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="9f250-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="9f250-125">可以使用以下方法来添加包：</span><span class="sxs-lookup"><span data-stu-id="9f250-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f250-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f250-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9f250-127">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="9f250-127">PMC option to install packages</span></span>

* <span data-ttu-id="9f250-128">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="9f250-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9f250-129">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录。</span><span class="sxs-lookup"><span data-stu-id="9f250-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="9f250-130">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="9f250-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="9f250-131">对 Google.Protobuf and Grpc.Tools 重复操作 `Install-Package`</span><span class="sxs-lookup"><span data-stu-id="9f250-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9f250-132">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="9f250-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9f250-133">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目</span><span class="sxs-lookup"><span data-stu-id="9f250-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="9f250-134">将“包源”设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="9f250-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="9f250-135">在搜索框中输入“Grpc.Core”</span><span class="sxs-lookup"><span data-stu-id="9f250-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="9f250-136">从“浏览”选项卡中选择“Grpc.Core”包，然后单击“安装”</span><span class="sxs-lookup"><span data-stu-id="9f250-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="9f250-137">为 Google.Protobuf 和 Grpc.Tools 重复上述操作</span><span class="sxs-lookup"><span data-stu-id="9f250-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9f250-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f250-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9f250-139">从“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9f250-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

<span data-ttu-id="9f250-140">为 Google.Protobuf 和 Grpc.Tools 重复上述操作</span><span class="sxs-lookup"><span data-stu-id="9f250-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9f250-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9f250-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9f250-142">右键单击“Solution Pad” > “添加包...”中的“包”文件夹</span><span class="sxs-lookup"><span data-stu-id="9f250-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="9f250-143">将“添加包”窗口的“源”下拉列表设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="9f250-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="9f250-144">在搜索框中输入“Grpc.Core”</span><span class="sxs-lookup"><span data-stu-id="9f250-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="9f250-145">从结果窗格中选择“Grpc.Core”包，然后单击“添加包”</span><span class="sxs-lookup"><span data-stu-id="9f250-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="9f250-146">为 Google.Protobuf 和 Grpc.Tools 重复上述操作</span><span class="sxs-lookup"><span data-stu-id="9f250-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="9f250-147">添加 greet.proto 文件</span><span class="sxs-lookup"><span data-stu-id="9f250-147">Add the greet.proto file</span></span>

<span data-ttu-id="9f250-148">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目。</span><span class="sxs-lookup"><span data-stu-id="9f250-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="9f250-149">将 greet.proto 文件添加到 GrpcGreeterClient 项目文件的 `<Protobuf>` 物料组：</span><span class="sxs-lookup"><span data-stu-id="9f250-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="9f250-150">可右键单击项目并从下拉菜单中选择“编辑 GrpcGreeterClient.csproj”选项来打开 GrpcGreeterClient 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="9f250-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="9f250-152">此外，也可以导航到 GrpcGreeterClient 目录并使用喜欢的编辑器编辑 `GrpcGreeterClient.csproj`。</span><span class="sxs-lookup"><span data-stu-id="9f250-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="9f250-153">添加 `GrpcServices="Client"` 属性，以便仅为包含的 protobuf 文件生成 C# 客户端资产。</span><span class="sxs-lookup"><span data-stu-id="9f250-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="9f250-154">生成客户端项目以触发 C# 客户端资产的生成。</span><span class="sxs-lookup"><span data-stu-id="9f250-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="9f250-155">创建 GreeterClient 并调用 SayHello 一元调用</span><span class="sxs-lookup"><span data-stu-id="9f250-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="9f250-156">将以下代码添加到 gRPC 客户端项目 `Program.cs` 文件的 `Main` 方法中：</span><span class="sxs-lookup"><span data-stu-id="9f250-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="9f250-157">若要访问所需类型，需要使用以下 using 语句：</span><span class="sxs-lookup"><span data-stu-id="9f250-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="9f250-158">GreeterClient 是通过实例化 `Channel` 创建的，后者包含用于创建与 gRPC 服务的连接的信息并用其来构造 `GreeterClient`：</span><span class="sxs-lookup"><span data-stu-id="9f250-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="9f250-159">GreeterClient 包含可以异步调用的一元调用 `SayHello`：</span><span class="sxs-lookup"><span data-stu-id="9f250-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="9f250-160">`SayHello` 调用的结果存储在 `reply` 中，之后可显示：</span><span class="sxs-lookup"><span data-stu-id="9f250-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="9f250-161">当操作完成并发布所有资源时，应关闭客户端使用的 `Channel`：</span><span class="sxs-lookup"><span data-stu-id="9f250-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="9f250-162">需要在 Greeter 命名空间中的类型可解析之前生成项目。</span><span class="sxs-lookup"><span data-stu-id="9f250-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="9f250-163">这些类型在项目生成期间自动生成，在项目生成过程运行之前不可用。</span><span class="sxs-lookup"><span data-stu-id="9f250-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9f250-164">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="9f250-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f250-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f250-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9f250-166">确保上一个教程中创建的 Greeter 服务正在运行。</span><span class="sxs-lookup"><span data-stu-id="9f250-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="9f250-167">服务运行后，返回到 GrpcGreeterClient 项目，将其设置为启动项目。</span><span class="sxs-lookup"><span data-stu-id="9f250-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="9f250-168">按 Ctrl+F5 以在不使用调试程序的情况下运行客户端。</span><span class="sxs-lookup"><span data-stu-id="9f250-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="9f250-169">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="9f250-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="9f250-170">该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。</span><span class="sxs-lookup"><span data-stu-id="9f250-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/client.png)

  <span data-ttu-id="9f250-172">该服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f250-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9f250-174">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9f250-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9f250-175">确保上一个教程中创建的 Greeter 服务正在运行。</span><span class="sxs-lookup"><span data-stu-id="9f250-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="9f250-176">使用 `dotnet run` 从单独的命令行运行客户端项目 GrpcGreeter.Client。</span><span class="sxs-lookup"><span data-stu-id="9f250-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="9f250-177">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="9f250-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="9f250-178">该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。</span><span class="sxs-lookup"><span data-stu-id="9f250-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9f250-179">该服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f250-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="9f250-180">检查 gRPC 项目的项目文件</span><span class="sxs-lookup"><span data-stu-id="9f250-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="9f250-181">gRPC 客户端 GrpcGreeterClient 文件：</span><span class="sxs-lookup"><span data-stu-id="9f250-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="9f250-182">Program.cs 包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f250-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f250-183">其他资源</span><span class="sxs-lookup"><span data-stu-id="9f250-183">Additional resources</span></span>

<span data-ttu-id="9f250-184">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9f250-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f250-185">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="9f250-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="9f250-186">针对上一个教程中创建的 gRPC Greeter 服务运行该服务。</span><span class="sxs-lookup"><span data-stu-id="9f250-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="9f250-187">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="9f250-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9f250-188">上一篇：创建 gRPC Greeter 服务</span><span class="sxs-lookup"><span data-stu-id="9f250-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
