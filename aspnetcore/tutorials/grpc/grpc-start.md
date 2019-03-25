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
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="b09ed-104">教程：开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="b09ed-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="b09ed-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b09ed-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b09ed-106">本教程介绍在 ASP.NET Core 上构建 gRPC 服务的基础知识。</span><span class="sxs-lookup"><span data-stu-id="b09ed-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="b09ed-107">在结束时，您将拥有回显问候语的 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="b09ed-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b09ed-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b09ed-109">创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="b09ed-110">运行服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-110">Run the service.</span></span>
> * <span data-ttu-id="b09ed-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="b09ed-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="b09ed-112">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="b09ed-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b09ed-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b09ed-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b09ed-114">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="b09ed-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b09ed-115">创建新的 ASP.NET Core Web 应用呈现。</span><span class="sxs-lookup"><span data-stu-id="b09ed-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="b09ed-116">![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="b09ed-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="b09ed-117">将项目命名为 GrpcGreeter。</span><span class="sxs-lookup"><span data-stu-id="b09ed-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="b09ed-118">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="b09ed-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="b09ed-119">![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="b09ed-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="b09ed-120">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”。</span><span class="sxs-lookup"><span data-stu-id="b09ed-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="b09ed-121">选择“gRPC 服务”模板。</span><span class="sxs-lookup"><span data-stu-id="b09ed-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="b09ed-122">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="b09ed-122">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b09ed-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b09ed-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b09ed-125">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="b09ed-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b09ed-126">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b09ed-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b09ed-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b09ed-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="b09ed-128">`dotnet new` 命令将在 GrpcGreeter 文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="b09ed-129">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b09ed-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="b09ed-130">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="b09ed-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="b09ed-131">选择“是”</span><span class="sxs-lookup"><span data-stu-id="b09ed-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b09ed-132">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b09ed-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b09ed-133">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b09ed-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="b09ed-134">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="b09ed-135">打开项目</span><span class="sxs-lookup"><span data-stu-id="b09ed-135">Open the project</span></span>

<span data-ttu-id="b09ed-136">在 Visual Studio 中，选择“文件”>“打开”，然后选择“GrpcGreeter.sln”文件。</span><span class="sxs-lookup"><span data-stu-id="b09ed-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="test-the-service"></a><span data-ttu-id="b09ed-137">测试服务</span><span class="sxs-lookup"><span data-stu-id="b09ed-137">Test the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b09ed-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b09ed-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b09ed-139">请确保 GrpcGreeter.Server 已设为启动项目，然后按 Ctrl+F5 以在不使用调试器的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-139">Ensure the **GrpcGreeter.Server** is set as the Startup Project and press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="b09ed-140">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="b09ed-141">日志显示该服务已开始侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="b09ed-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_start.png)

* <span data-ttu-id="b09ed-143">服务开始运行后，将 GrpcGreeter.Client 设为启动项目，然后按 Ctrl+F5 以在不使用调试器的情况下运行客户端。</span><span class="sxs-lookup"><span data-stu-id="b09ed-143">Once the service is running, set the **GrpcGreeter.Client** is set as the Startup Project and press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="b09ed-144">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="b09ed-144">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="b09ed-145">该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。</span><span class="sxs-lookup"><span data-stu-id="b09ed-145">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/client.png)

  <span data-ttu-id="b09ed-147">该服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="b09ed-147">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b09ed-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b09ed-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b09ed-150">使用 `dotnet run` 从命令行运行服务器项目 GrpcGreeter.Server。</span><span class="sxs-lookup"><span data-stu-id="b09ed-150">Run the Server project GrpcGreeter.Server from the command line using `dotnet run`.</span></span> <span data-ttu-id="b09ed-151">日志显示该服务已开始侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="b09ed-151">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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

* <span data-ttu-id="b09ed-152">使用 `dotnet run` 从单独的命令行运行客户端项目 GrpcGreeter.Client。</span><span class="sxs-lookup"><span data-stu-id="b09ed-152">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="b09ed-153">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="b09ed-153">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="b09ed-154">该服务将发送一条消息“Hello GreeterClient”作为响应，并显示在命令提示符中。</span><span class="sxs-lookup"><span data-stu-id="b09ed-154">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="b09ed-155">该服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="b09ed-155">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="b09ed-156">检查 gRPC 项目的项目文件</span><span class="sxs-lookup"><span data-stu-id="b09ed-156">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="b09ed-157">GrpcGreeter.Server 文件：</span><span class="sxs-lookup"><span data-stu-id="b09ed-157">GrpcGreeter.Server files:</span></span>

* <span data-ttu-id="b09ed-158">greet.proto：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="b09ed-158">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="b09ed-159">有关更多信息，请参见<xref:grpc/index>。</span><span class="sxs-lookup"><span data-stu-id="b09ed-159">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="b09ed-160">Services 文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="b09ed-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="b09ed-161">appSettings.json：包含配置数据，如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="b09ed-161">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="b09ed-162">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="b09ed-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="b09ed-163">Program.cs:包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="b09ed-163">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="b09ed-164">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="b09ed-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="b09ed-165">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b09ed-165">Startup.cs</span></span>

<span data-ttu-id="b09ed-166">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="b09ed-166">Contains code that configures app behavior.</span></span> <span data-ttu-id="b09ed-167">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="b09ed-167">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="b09ed-168">gRPC 客户端 GrpcGreeter.Client 文件：</span><span class="sxs-lookup"><span data-stu-id="b09ed-168">gRPC client GrpcGreeter.Client file:</span></span>

<span data-ttu-id="b09ed-169">Program.cs 包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="b09ed-169">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="b09ed-170">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b09ed-170">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b09ed-171">创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b09ed-171">Created a gRPC service.</span></span>
> * <span data-ttu-id="b09ed-172">运行服务和客户端以对服务进行测试。</span><span class="sxs-lookup"><span data-stu-id="b09ed-172">Ran the service and a client to test the service.</span></span>
> * <span data-ttu-id="b09ed-173">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="b09ed-173">Examined the project files.</span></span>
