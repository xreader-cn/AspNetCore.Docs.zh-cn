---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 496f659bd51e2404a936bea8aad77e674e1a285d
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022497"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="37148-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="37148-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="37148-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="37148-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="37148-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="37148-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="37148-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="37148-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="37148-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="37148-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="37148-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="37148-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="37148-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="37148-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="37148-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="37148-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="37148-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="37148-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="37148-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="37148-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="37148-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37148-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="37148-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="37148-117">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="37148-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="37148-119">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="37148-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="37148-120">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="37148-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="37148-121">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="37148-121">Select **Next**</span></span>
* <span data-ttu-id="37148-122">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="37148-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="37148-123">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="37148-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="37148-124">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="37148-124">Select **Create**</span></span>
* <span data-ttu-id="37148-125">在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="37148-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="37148-126">在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="37148-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="37148-127">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="37148-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="37148-128">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="37148-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="37148-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37148-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="37148-130">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="37148-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="37148-131">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="37148-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="37148-132">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="37148-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="37148-133">`dotnet new` 命令将在 GrpcGreeter  文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="37148-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="37148-134">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="37148-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="37148-135">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="37148-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="37148-136">选择“是” </span><span class="sxs-lookup"><span data-stu-id="37148-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="37148-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37148-138">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="37148-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="37148-139">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="37148-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="37148-140">打开项目</span><span class="sxs-lookup"><span data-stu-id="37148-140">Open the project</span></span>

<span data-ttu-id="37148-141">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“GrpcGreeter.sln”  文件。</span><span class="sxs-lookup"><span data-stu-id="37148-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="37148-142">运行服务</span><span class="sxs-lookup"><span data-stu-id="37148-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="37148-144">按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="37148-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="37148-145">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="37148-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="37148-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="37148-147">使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="37148-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="37148-148">日志显示该服务正在侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="37148-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="37148-149">gRPC 模板配置为使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。</span><span class="sxs-lookup"><span data-stu-id="37148-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="37148-150">gRPC 客户端需要使用 HTTPS 调用服务器。</span><span class="sxs-lookup"><span data-stu-id="37148-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="37148-151">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="37148-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="37148-152">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="37148-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="37148-153">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="37148-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="37148-154">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="37148-154">Examine the project files</span></span>

<span data-ttu-id="37148-155">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="37148-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="37148-156">*greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="37148-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="37148-157">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="37148-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="37148-158">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="37148-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="37148-159">*appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="37148-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="37148-160">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="37148-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="37148-161">Program.cs  :包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="37148-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="37148-162">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="37148-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="37148-163">*Startup.cs*：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="37148-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="37148-164">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="37148-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="37148-165">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="37148-165">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="37148-167">打开 Visual Studio 的第二个实例。</span><span class="sxs-lookup"><span data-stu-id="37148-167">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="37148-168">从菜单栏中选择“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="37148-168">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="37148-169">在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。</span><span class="sxs-lookup"><span data-stu-id="37148-169">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="37148-170">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="37148-170">Select **Next**</span></span>
* <span data-ttu-id="37148-171">在“名称”文本框中，输入“GrpcGreeterClient”  。</span><span class="sxs-lookup"><span data-stu-id="37148-171">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="37148-172">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="37148-172">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="37148-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37148-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="37148-174">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="37148-174">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="37148-175">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="37148-175">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="37148-176">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="37148-176">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="37148-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="37148-178">按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用  。</span><span class="sxs-lookup"><span data-stu-id="37148-178">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="37148-179">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="37148-179">Add required packages</span></span>

<span data-ttu-id="37148-180">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="37148-180">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="37148-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="37148-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="37148-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="37148-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="37148-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="37148-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="37148-184">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="37148-184">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-185">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37148-186">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="37148-186">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="37148-187">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="37148-187">PMC option to install packages</span></span>

* <span data-ttu-id="37148-188">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="37148-188">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="37148-189">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。</span><span class="sxs-lookup"><span data-stu-id="37148-189">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="37148-190">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="37148-190">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="37148-191">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="37148-191">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="37148-192">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="37148-192">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="37148-193">选择“浏览”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="37148-193">Select the **Browse** tab.</span></span>
* <span data-ttu-id="37148-194">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="37148-194">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="37148-195">从“浏览”选项卡中选择“Grpc.Net.Client”包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="37148-195">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="37148-196">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="37148-196">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="37148-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37148-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="37148-198">从“集成终端”运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="37148-198">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="37148-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="37148-200">右键单击“Solution Pad” > “添加包...”中的“包”文件夹   </span><span class="sxs-lookup"><span data-stu-id="37148-200">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="37148-201">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="37148-201">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="37148-202">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”  </span><span class="sxs-lookup"><span data-stu-id="37148-202">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="37148-203">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="37148-203">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="37148-204">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="37148-204">Add greet.proto</span></span>

* <span data-ttu-id="37148-205">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="37148-205">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="37148-206">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="37148-206">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="37148-207">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="37148-207">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-208">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="37148-209">右键单击项目，并选择“编辑项目文件”  。</span><span class="sxs-lookup"><span data-stu-id="37148-209">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="37148-210">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37148-210">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="37148-211">选择 GrpcGreeterClient.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="37148-211">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="37148-212">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="37148-213">右键单击项目，并选择“工具”>“编辑文件”  。</span><span class="sxs-lookup"><span data-stu-id="37148-213">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="37148-214">添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="37148-214">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="37148-215">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="37148-215">Create the Greeter client</span></span>

<span data-ttu-id="37148-216">构建项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="37148-216">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="37148-217">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="37148-217">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="37148-218">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="37148-218">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="37148-219">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="37148-219">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="37148-220">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="37148-220">The Greeter client is created by:</span></span>

* <span data-ttu-id="37148-221">实例化 `HttpClient`，其包含用于创建与 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="37148-221">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="37148-222">使用 `HttpClient` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="37148-222">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="37148-223">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="37148-223">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="37148-224">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="37148-224">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="37148-225">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="37148-225">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37148-226">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37148-226">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="37148-227">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="37148-227">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="37148-228">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动客户端。</span><span class="sxs-lookup"><span data-stu-id="37148-228">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="37148-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="37148-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="37148-230">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="37148-230">Start the Greeter service.</span></span>
* <span data-ttu-id="37148-231">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="37148-231">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="37148-232">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="37148-232">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="37148-233">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="37148-233">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="37148-234">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="37148-234">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="37148-235">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="37148-235">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="37148-236">后续步骤</span><span class="sxs-lookup"><span data-stu-id="37148-236">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
