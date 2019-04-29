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
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="65173-104">教程：开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="65173-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="65173-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="65173-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="65173-106">本教程介绍在 ASP.NET Core 上构建 gRPC 服务的基础知识。</span><span class="sxs-lookup"><span data-stu-id="65173-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="65173-107">在结束时，您将拥有回显问候语的 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="65173-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="65173-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="65173-109">创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="65173-110">运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-110">Run the gRPC service.</span></span>
> * <span data-ttu-id="65173-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="65173-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="65173-112">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="65173-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65173-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65173-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65173-114">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="65173-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="65173-115">创建新的 ASP.NET Core Web 应用呈现。</span><span class="sxs-lookup"><span data-stu-id="65173-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="65173-116">![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="65173-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="65173-117">将项目命名为 GrpcGreeter。</span><span class="sxs-lookup"><span data-stu-id="65173-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="65173-118">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="65173-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="65173-119">![新建 ASP.NET Core Web 应用程序](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="65173-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="65173-120">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”。</span><span class="sxs-lookup"><span data-stu-id="65173-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="65173-121">选择“gRPC 服务”模板。</span><span class="sxs-lookup"><span data-stu-id="65173-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="65173-122">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="65173-122">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65173-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65173-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65173-125">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="65173-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="65173-126">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="65173-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="65173-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="65173-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="65173-128">`dotnet new` 命令将在 GrpcGreeter 文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="65173-129">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹。</span><span class="sxs-lookup"><span data-stu-id="65173-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="65173-130">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="65173-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="65173-131">选择“是”</span><span class="sxs-lookup"><span data-stu-id="65173-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="65173-132">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65173-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="65173-133">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="65173-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="65173-134">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="65173-135">打开项目</span><span class="sxs-lookup"><span data-stu-id="65173-135">Open the project</span></span>

<span data-ttu-id="65173-136">在 Visual Studio 中，选择“文件”>“打开”，然后选择“GrpcGreeter.sln”文件。</span><span class="sxs-lookup"><span data-stu-id="65173-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="65173-137">运行服务</span><span class="sxs-lookup"><span data-stu-id="65173-137">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65173-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65173-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65173-139">按 Ctrl+F5 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-139">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="65173-140">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="65173-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="65173-141">日志显示该服务已开始侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="65173-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![新建 ASP.NET Core Web 应用程序](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="65173-143">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65173-143">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="65173-144">使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter。</span><span class="sxs-lookup"><span data-stu-id="65173-144">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span> <span data-ttu-id="65173-145">日志显示该服务已开始侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="65173-145">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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

<span data-ttu-id="65173-146">下一个教程将演示如何生成 gRPC 客户端，这是测试 Greeter 服务所必须执行的步骤。</span><span class="sxs-lookup"><span data-stu-id="65173-146">The next tutorial will demonstrate how to build a gRPC client, which is required to test the Greeter service.</span></span>

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="65173-147">检查 gRPC 项目的项目文件</span><span class="sxs-lookup"><span data-stu-id="65173-147">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="65173-148">GrpcGreeter 文件：</span><span class="sxs-lookup"><span data-stu-id="65173-148">GrpcGreeter files:</span></span>

* <span data-ttu-id="65173-149">greet.proto：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="65173-149">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="65173-150">有关更多信息，请参见<xref:grpc/index>。</span><span class="sxs-lookup"><span data-stu-id="65173-150">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="65173-151">Services 文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="65173-151">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="65173-152">appSettings.json：包含配置数据，如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="65173-152">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="65173-153">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="65173-153">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="65173-154">Program.cs:包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="65173-154">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="65173-155">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="65173-155">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="65173-156">Startup.cs：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="65173-156">Startup.cs: Contains code that configures app behavior.</span></span> <span data-ttu-id="65173-157">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="65173-157">For more information, see <xref:fundamentals/startup>.</span></span>

### <a name="test-the-service"></a><span data-ttu-id="65173-158">测试服务</span><span class="sxs-lookup"><span data-stu-id="65173-158">Test the service</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65173-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="65173-159">Additional resources</span></span>

<span data-ttu-id="65173-160">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="65173-160">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="65173-161">创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-161">Created a gRPC service.</span></span>
> * <span data-ttu-id="65173-162">运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="65173-162">Ran the gRPC service.</span></span>
> * <span data-ttu-id="65173-163">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="65173-163">Examined the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="65173-164">下一篇：创建 .NET Core gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="65173-164">Next: Create a .NET Core gRPC client</span></span>](xref:tutorials/grpc/grpc-client)