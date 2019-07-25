---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 3e90e3b17186757fe157fb6641888786bb7a0df2
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412521"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="73ef4-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="73ef4-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="73ef4-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="73ef4-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="73ef4-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="73ef4-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="73ef4-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="73ef4-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="73ef4-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="73ef4-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="73ef4-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="73ef4-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="73ef4-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="73ef4-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="73ef4-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="73ef4-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="73ef4-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="73ef4-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="73ef4-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73ef4-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73ef4-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73ef4-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="73ef4-117">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="73ef4-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73ef4-119">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="73ef4-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="73ef4-120">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="73ef4-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="73ef4-121">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="73ef4-121">Select **Next**</span></span>
* <span data-ttu-id="73ef4-122">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="73ef4-123">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="73ef4-124">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="73ef4-124">Select **Create**</span></span>
* <span data-ttu-id="73ef4-125">在“创建新的 ASP.NET Core Web 应用程序”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="73ef4-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="73ef4-126">在下拉菜单中选择 .NET Core 和 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="73ef4-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="73ef4-127">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="73ef4-128">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="73ef4-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73ef4-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73ef4-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="73ef4-130">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="73ef4-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="73ef4-131">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="73ef4-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="73ef4-132">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="73ef4-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="73ef4-133">`dotnet new` 命令将在 GrpcGreeter  文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="73ef4-134">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="73ef4-135">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="73ef4-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="73ef4-136">选择“是” </span><span class="sxs-lookup"><span data-stu-id="73ef4-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73ef4-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="73ef4-138">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="73ef4-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="73ef4-139">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="73ef4-140">打开项目</span><span class="sxs-lookup"><span data-stu-id="73ef4-140">Open the project</span></span>

<span data-ttu-id="73ef4-141">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“GrpcGreeter.sln”  文件。</span><span class="sxs-lookup"><span data-stu-id="73ef4-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="73ef4-142">运行服务</span><span class="sxs-lookup"><span data-stu-id="73ef4-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73ef4-144">按 `Ctrl+F5` 以在不使用调试程序的情况下运行 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="73ef4-145">Visual Studio 在命令提示符中运行该服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="73ef4-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="73ef4-147">使用 `dotnet run` 从命令行运行 gRPC Greeter 项目 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="73ef4-148">日志显示该服务正在侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="73ef4-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="73ef4-149">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="73ef4-149">Examine the project files</span></span>

<span data-ttu-id="73ef4-150">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="73ef4-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="73ef4-151">*greet.proto*：*Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="73ef4-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="73ef4-152">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="73ef4-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="73ef4-153">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="73ef4-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="73ef4-154">*appSettings.json*：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="73ef4-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="73ef4-155">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="73ef4-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="73ef4-156">Program.cs  :包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="73ef4-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="73ef4-157">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="73ef4-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="73ef4-158">*Startup.cs*：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="73ef4-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="73ef4-159">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="73ef4-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="73ef4-160">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="73ef4-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73ef4-162">打开 Visual Studio 的第二个实例。</span><span class="sxs-lookup"><span data-stu-id="73ef4-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="73ef4-163">从菜单栏中选择“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="73ef4-164">在“新建项目”对话框中，选择“控制台应用(.NET Core)”   。</span><span class="sxs-lookup"><span data-stu-id="73ef4-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="73ef4-165">选择“下一步” </span><span class="sxs-lookup"><span data-stu-id="73ef4-165">Select **Next**</span></span>
* <span data-ttu-id="73ef4-166">在“名称”文本框中，输入“GrpcGreeterClient”  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="73ef4-167">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73ef4-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73ef4-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="73ef4-169">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="73ef4-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="73ef4-170">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="73ef4-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="73ef4-171">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="73ef4-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73ef4-172">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="73ef4-173">按照[此处](/dotnet/core/tutorials/using-on-mac-vs-full-solution)的说明创建名为 GrpcGreeterClient 的控制台应用  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="73ef4-174">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="73ef4-174">Add required packages</span></span>

<span data-ttu-id="73ef4-175">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="73ef4-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="73ef4-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="73ef4-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="73ef4-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="73ef4-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="73ef4-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="73ef4-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="73ef4-179">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="73ef4-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73ef4-181">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="73ef4-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="73ef4-182">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="73ef4-182">PMC option to install packages</span></span>

* <span data-ttu-id="73ef4-183">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="73ef4-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="73ef4-184">从“包管理器控制台”窗口中，导航到有 GrpcGreeterClient.csproj 文件所在的目录   。</span><span class="sxs-lookup"><span data-stu-id="73ef4-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="73ef4-185">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="73ef4-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="73ef4-186">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="73ef4-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="73ef4-187">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="73ef4-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="73ef4-188">选择“浏览”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="73ef4-189">在搜索框中输入 Grpc.Core  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="73ef4-190">从“浏览”选项卡中选择 Grpc.Core 包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="73ef4-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="73ef4-191">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="73ef4-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73ef4-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73ef4-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73ef4-193">从“集成终端”运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="73ef4-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73ef4-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="73ef4-195">右键单击“Solution Pad” > “添加包...”中的“包”文件夹   </span><span class="sxs-lookup"><span data-stu-id="73ef4-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="73ef4-196">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="73ef4-197">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”  </span><span class="sxs-lookup"><span data-stu-id="73ef4-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="73ef4-198">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="73ef4-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="73ef4-199">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="73ef4-199">Add greet.proto</span></span>

* <span data-ttu-id="73ef4-200">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="73ef4-201">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="73ef4-202">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="73ef4-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="73ef4-204">右键单击项目，并选择“编辑项目文件”  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73ef4-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73ef4-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="73ef4-206">选择 GrpcGreeterClient.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73ef4-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="73ef4-208">右键单击项目，并选择“工具”>“编辑文件”  。</span><span class="sxs-lookup"><span data-stu-id="73ef4-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="73ef4-209">添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="73ef4-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="73ef4-210">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="73ef4-210">Create the Greeter client</span></span>

<span data-ttu-id="73ef4-211">构建项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="73ef4-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="73ef4-212">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="73ef4-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="73ef4-213">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="73ef4-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="73ef4-214">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="73ef4-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="73ef4-215">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="73ef4-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="73ef4-216">实例化 `HttpClient`，其包含用于创建与 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="73ef4-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="73ef4-217">使用 `HttpClient` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="73ef4-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="73ef4-218">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="73ef4-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="73ef4-219">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="73ef4-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="73ef4-220">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="73ef4-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73ef4-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73ef4-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73ef4-222">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="73ef4-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="73ef4-223">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="73ef4-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="73ef4-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73ef4-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="73ef4-225">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="73ef4-225">Start the Greeter service.</span></span>
* <span data-ttu-id="73ef4-226">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="73ef4-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="73ef4-227">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="73ef4-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="73ef4-228">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="73ef4-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="73ef4-229">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="73ef4-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="73ef4-230">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="73ef4-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="73ef4-231">后续步骤</span><span class="sxs-lookup"><span data-stu-id="73ef4-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
