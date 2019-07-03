---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 6aef56ecd61ad71e166c03c12b28b25b931cdd88
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152925"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="c33c8-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="c33c8-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="c33c8-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="c33c8-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="c33c8-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="c33c8-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="c33c8-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="c33c8-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="c33c8-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c33c8-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c33c8-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="c33c8-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c33c8-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="c33c8-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="c33c8-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="c33c8-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="c33c8-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="c33c8-113">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="c33c8-113">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c33c8-115">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="c33c8-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c33c8-116">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="c33c8-116">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c33c8-117">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="c33c8-117">Select **Next**</span></span>
* <span data-ttu-id="c33c8-118">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-118">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="c33c8-119">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-119">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="c33c8-120">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="c33c8-120">Select **Create**</span></span>
* <span data-ttu-id="c33c8-121">在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="c33c8-121">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="c33c8-122">在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="c33c8-122">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="c33c8-123">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-123">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="c33c8-124">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="c33c8-124">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c33c8-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c33c8-125">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c33c8-126">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="c33c8-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c33c8-127">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c33c8-127">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c33c8-128">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c33c8-128">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="c33c8-129">`dotnet new` 命令将在 GrpcGreeter  文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-129">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="c33c8-130">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-130">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="c33c8-131">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="c33c8-131">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="c33c8-132">选择“是” </span><span class="sxs-lookup"><span data-stu-id="c33c8-132">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c33c8-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c33c8-134">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c33c8-134">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="c33c8-135">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-135">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="c33c8-136">打开项目</span><span class="sxs-lookup"><span data-stu-id="c33c8-136">Open the project</span></span>

<span data-ttu-id="c33c8-137">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“GrpcGreeter.sln”  文件。</span><span class="sxs-lookup"><span data-stu-id="c33c8-137">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="c33c8-138">运行服务</span><span class="sxs-lookup"><span data-stu-id="c33c8-138">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-139">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c33c8-140">按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-140">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="c33c8-141">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-141">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c33c8-142">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-142">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c33c8-143">使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-143">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="c33c8-144">日志显示该服务正在侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="c33c8-144">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="c33c8-145">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="c33c8-145">Examine the project files</span></span>

<span data-ttu-id="c33c8-146">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="c33c8-146">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="c33c8-147">*greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="c33c8-147">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="c33c8-148">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="c33c8-148">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="c33c8-149">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="c33c8-149">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="c33c8-150">*appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="c33c8-150">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="c33c8-151">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="c33c8-151">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="c33c8-152">Program.cs  :包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="c33c8-152">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="c33c8-153">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="c33c8-153">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="c33c8-154">*Startup.cs*：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="c33c8-154">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="c33c8-155">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="c33c8-155">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="c33c8-156">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="c33c8-156">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c33c8-158">打开 Visual Studio 的第二个实例。</span><span class="sxs-lookup"><span data-stu-id="c33c8-158">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="c33c8-159">从菜单栏中选择“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-159">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="c33c8-160">在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。</span><span class="sxs-lookup"><span data-stu-id="c33c8-160">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="c33c8-161">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="c33c8-161">Select **Next**</span></span>
* <span data-ttu-id="c33c8-162">在“名称”文本框中，输入“GrpcGreeterClient”  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-162">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="c33c8-163">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-163">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c33c8-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c33c8-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c33c8-165">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="c33c8-165">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c33c8-166">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c33c8-166">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c33c8-167">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c33c8-167">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c33c8-168">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c33c8-169">按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-169">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="c33c8-170">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="c33c8-170">Add required packages</span></span>

<span data-ttu-id="c33c8-171">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="c33c8-171">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="c33c8-172">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="c33c8-172">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="c33c8-173">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="c33c8-173">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="c33c8-174">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="c33c8-174">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="c33c8-175">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="c33c8-175">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c33c8-177">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="c33c8-177">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="c33c8-178">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="c33c8-178">PMC option to install packages</span></span>

* <span data-ttu-id="c33c8-179">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="c33c8-179">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="c33c8-180">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。</span><span class="sxs-lookup"><span data-stu-id="c33c8-180">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="c33c8-181">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c33c8-181">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="c33c8-182">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="c33c8-182">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="c33c8-183">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="c33c8-183">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="c33c8-184">选择“浏览”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-184">Select the **Browse** tab.</span></span>
* <span data-ttu-id="c33c8-185">在搜索框中输入 Grpc.Core  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-185">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="c33c8-186">从“浏览”选项卡中选择 Grpc.Core 包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="c33c8-186">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="c33c8-187">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="c33c8-187">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c33c8-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c33c8-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c33c8-189">从“集成终端”运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="c33c8-189">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c33c8-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c33c8-191">右键单击“Solution Pad” > “添加包...”中的“包”文件夹   </span><span class="sxs-lookup"><span data-stu-id="c33c8-191">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="c33c8-192">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-192">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="c33c8-193">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”  </span><span class="sxs-lookup"><span data-stu-id="c33c8-193">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="c33c8-194">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="c33c8-194">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="c33c8-195">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="c33c8-195">Add greet.proto</span></span>

* <span data-ttu-id="c33c8-196">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-196">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="c33c8-197">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-197">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="c33c8-198">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="c33c8-198">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-199">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="c33c8-200">右键单击项目，并选择“编辑项目文件”  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-200">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c33c8-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c33c8-201">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="c33c8-202">选择 GrpcGreeterClient.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-202">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c33c8-203">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="c33c8-204">右键单击项目，并选择“工具”>“编辑文件”  。</span><span class="sxs-lookup"><span data-stu-id="c33c8-204">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="c33c8-205">添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="c33c8-205">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="c33c8-206">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="c33c8-206">Create the Greeter client</span></span>

<span data-ttu-id="c33c8-207">构建项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="c33c8-207">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="c33c8-208">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="c33c8-208">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="c33c8-209">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="c33c8-209">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="c33c8-210">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="c33c8-210">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="c33c8-211">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="c33c8-211">The Greeter client is created by:</span></span>

* <span data-ttu-id="c33c8-212">实例化 `HttpClient`，其包含用于创建与 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="c33c8-212">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="c33c8-213">使用 `HttpClient` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="c33c8-213">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="c33c8-214">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="c33c8-214">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="c33c8-215">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="c33c8-215">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="c33c8-216">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="c33c8-216">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c33c8-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c33c8-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c33c8-218">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="c33c8-218">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="c33c8-219">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="c33c8-219">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c33c8-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c33c8-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c33c8-221">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="c33c8-221">Start the Greeter service.</span></span>
* <span data-ttu-id="c33c8-222">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="c33c8-222">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="c33c8-223">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="c33c8-223">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="c33c8-224">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="c33c8-224">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="c33c8-225">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="c33c8-225">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="c33c8-226">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c33c8-226">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a><span data-ttu-id="c33c8-227">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c33c8-227">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
