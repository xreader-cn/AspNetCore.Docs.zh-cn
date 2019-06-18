---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 919db3f31310342657c89100a6e25e8293648a9f
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034805"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="ca5cb-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="ca5cb-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="ca5cb-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ca5cb-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ca5cb-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="ca5cb-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="ca5cb-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ca5cb-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ca5cb-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="ca5cb-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="ca5cb-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="ca5cb-113">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="ca5cb-113">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca5cb-115">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ca5cb-116">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-116">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ca5cb-117">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="ca5cb-117">Select **Next**</span></span>
* <span data-ttu-id="ca5cb-118">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-118">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ca5cb-119">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-119">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="ca5cb-120">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="ca5cb-120">Select **Create**</span></span>
* <span data-ttu-id="ca5cb-121">在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-121">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="ca5cb-122">在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-122">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="ca5cb-123">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-123">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="ca5cb-124">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="ca5cb-124">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca5cb-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca5cb-125">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ca5cb-126">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ca5cb-127">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-127">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ca5cb-128">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-128">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ca5cb-129">`dotnet new` 命令将在 GrpcGreeter  文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-129">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ca5cb-130">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-130">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ca5cb-131">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="ca5cb-131">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ca5cb-132">选择“是” </span><span class="sxs-lookup"><span data-stu-id="ca5cb-132">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ca5cb-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ca5cb-134">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-134">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="ca5cb-135">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-135">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ca5cb-136">打开项目</span><span class="sxs-lookup"><span data-stu-id="ca5cb-136">Open the project</span></span>

<span data-ttu-id="ca5cb-137">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“GrpcGreeter.sln”  文件。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-137">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="ca5cb-138">运行服务</span><span class="sxs-lookup"><span data-stu-id="ca5cb-138">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-139">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca5cb-140">按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-140">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="ca5cb-141">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-141">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ca5cb-142">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-142">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ca5cb-143">使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-143">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="ca5cb-144">日志显示该服务正在侦听 `http://localhost:50051`。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-144">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="ca5cb-145">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="ca5cb-145">Examine the project files</span></span>

<span data-ttu-id="ca5cb-146">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-146">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="ca5cb-147">*greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-147">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ca5cb-148">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-148">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="ca5cb-149">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-149">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ca5cb-150">*appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-150">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ca5cb-151">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-151">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ca5cb-152">Program.cs  :包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-152">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ca5cb-153">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-153">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="ca5cb-154">*Startup.cs*：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-154">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="ca5cb-155">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-155">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="ca5cb-156">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="ca5cb-156">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca5cb-158">从菜单栏中选择“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-158">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="ca5cb-159">在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-159">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="ca5cb-160">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="ca5cb-160">Select **Next**</span></span>
* <span data-ttu-id="ca5cb-161">在“名称”文本框中，输入“GrpcGreeterClient”  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-161">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="ca5cb-162">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-162">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca5cb-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca5cb-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ca5cb-164">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ca5cb-165">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-165">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ca5cb-166">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-166">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ca5cb-167">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ca5cb-168">按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-168">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="ca5cb-169">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="ca5cb-169">Add required packages</span></span>

<span data-ttu-id="ca5cb-170">将以下包添加到 gRPC 客户端项目：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-170">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="ca5cb-171">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-171">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="ca5cb-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="ca5cb-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="ca5cb-174">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-174">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ca5cb-176">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-176">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="ca5cb-177">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="ca5cb-177">PMC option to install packages</span></span>

* <span data-ttu-id="ca5cb-178">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="ca5cb-178">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="ca5cb-179">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-179">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="ca5cb-180">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-180">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="ca5cb-181">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="ca5cb-181">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="ca5cb-182">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="ca5cb-182">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="ca5cb-183">选择“浏览”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-183">Select the **Browse** tab.</span></span>
* <span data-ttu-id="ca5cb-184">在搜索框中输入 Grpc.Core  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-184">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="ca5cb-185">从“浏览”选项卡中选择 Grpc.Core 包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-185">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="ca5cb-186">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-186">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca5cb-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca5cb-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ca5cb-188">从“集成终端”运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-188">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ca5cb-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ca5cb-190">右键单击“Solution Pad” > “添加包...”中的“包”文件夹   </span><span class="sxs-lookup"><span data-stu-id="ca5cb-190">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="ca5cb-191">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ca5cb-192">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”  </span><span class="sxs-lookup"><span data-stu-id="ca5cb-192">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="ca5cb-193">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="ca5cb-194">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="ca5cb-194">Add greet.proto</span></span>

* <span data-ttu-id="ca5cb-195">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-195">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="ca5cb-196">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-196">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="ca5cb-197">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-197">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-198">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="ca5cb-199">右键单击项目并选择“编辑 GrpcGreeterClient.csproj”  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-199">Right-click the project and select the **Edit GrpcGreeterClient.csproj**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca5cb-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca5cb-200">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="ca5cb-201">选择 GrpcGreeterClient.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-201">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ca5cb-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="ca5cb-203">右键单击项目，并选择“工具”>“编辑文件”  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-203">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="ca5cb-204">将 greet.proto 文件添加到 GrpcGreeterClient 项目文件的 `<Protobuf>` 物料组  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-204">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

<span data-ttu-id="ca5cb-205">生成客户端项目以触发 C# 客户端资产的生成。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-205">Build the client project to trigger the generation of the C# client assets.</span></span>

### <a name="create-the-greeter-client"></a><span data-ttu-id="ca5cb-206">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="ca5cb-206">Create the Greeter client</span></span>

<span data-ttu-id="ca5cb-207">构建项目，在 Greeter 命名空间中创建类型  。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-207">Build the project to create the types in the **Greeter** namespace.</span></span> <span data-ttu-id="ca5cb-208">`Greeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-208">The `Greeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="ca5cb-209">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-209">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="ca5cb-210">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-210">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="ca5cb-211">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-211">The Greeter client is created by:</span></span>

* <span data-ttu-id="ca5cb-212">实例化 `HttpClient`，其包含用于创建与 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-212">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="ca5cb-213">使用 `HttpClient` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-213">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="ca5cb-214">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-214">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="ca5cb-215">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-215">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="ca5cb-216">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="ca5cb-216">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca5cb-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5cb-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ca5cb-218">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-218">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="ca5cb-219">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-219">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ca5cb-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ca5cb-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ca5cb-221">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-221">Start the Greeter service.</span></span>
* <span data-ttu-id="ca5cb-222">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-222">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="ca5cb-223">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-223">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="ca5cb-224">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-224">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="ca5cb-225">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="ca5cb-225">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="ca5cb-226">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ca5cb-226">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="ca5cb-227">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ca5cb-227">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
