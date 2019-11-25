---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
ms.author: johluo
ms.date: 11/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: e5373d9abb9a770132e756843dbd15534dbe3356
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116141"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="87e46-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="87e46-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="87e46-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="87e46-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="87e46-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="87e46-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="87e46-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="87e46-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="87e46-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="87e46-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="87e46-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="87e46-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="87e46-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="87e46-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="87e46-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="87e46-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="87e46-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="87e46-117">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="87e46-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="87e46-119">启动 Visual Studio 并选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="87e46-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="87e46-120">或者，从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="87e46-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="87e46-121">在“创建新项目”对话框中，选择“gRPC 服务”，然后选择“下一步”    ：</span><span class="sxs-lookup"><span data-stu-id="87e46-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![**创建新项目** 对话框](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="87e46-123">将项目命名为 GrpcGreeter  。</span><span class="sxs-lookup"><span data-stu-id="87e46-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="87e46-124">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="87e46-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="87e46-125">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="87e46-125">Select **Create**.</span></span>
* <span data-ttu-id="87e46-126">在“创建新 gRPC 服务”  对话框中：</span><span class="sxs-lookup"><span data-stu-id="87e46-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="87e46-127">选择“gRPC 服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="87e46-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="87e46-128">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="87e46-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="87e46-130">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="87e46-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="87e46-131">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="87e46-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="87e46-132">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="87e46-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="87e46-133">`dotnet new` 命令将在 GrpcGreeter  文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="87e46-134">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="87e46-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="87e46-135">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="87e46-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="87e46-136">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="87e46-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87e46-138">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="87e46-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="87e46-139">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="87e46-140">打开项目</span><span class="sxs-lookup"><span data-stu-id="87e46-140">Open the project</span></span>

<span data-ttu-id="87e46-141">在 Visual Studio 中，选择“文件” > “打开”，然后选择“GrpcGreeter.csproj”文件    。</span><span class="sxs-lookup"><span data-stu-id="87e46-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="87e46-142">运行服务</span><span class="sxs-lookup"><span data-stu-id="87e46-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="87e46-143">日志显示该服务正在侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="87e46-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="87e46-144">gRPC 模板配置为使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。</span><span class="sxs-lookup"><span data-stu-id="87e46-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="87e46-145">gRPC 客户端需要使用 HTTPS 调用服务器。</span><span class="sxs-lookup"><span data-stu-id="87e46-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="87e46-146">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="87e46-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="87e46-147">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="87e46-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="87e46-148">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="87e46-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="87e46-149">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="87e46-149">Examine the project files</span></span>

<span data-ttu-id="87e46-150">GrpcGreeter 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="87e46-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="87e46-151">*greet.proto* &ndash; Protos/greet.proto 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产  。</span><span class="sxs-lookup"><span data-stu-id="87e46-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="87e46-152">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="87e46-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="87e46-153">Services  文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="87e46-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="87e46-154">*appSettings.json* &ndash; 包含配置数据，如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="87e46-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="87e46-155">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="87e46-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="87e46-156">*Program.cs* &ndash; 包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="87e46-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="87e46-157">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="87e46-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="87e46-158">*Startup.cs* &ndash; 包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="87e46-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="87e46-159">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="87e46-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="87e46-160">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="87e46-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="87e46-162">打开 Visual Studio 的第二个实例并选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="87e46-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="87e46-163">在“创建新项目”对话框中，选择“控制台应用(.NET Core)”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="87e46-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="87e46-164">在“名称”文本框中，输入“GrpcGreeterClient”，然后选择“创建”    。</span><span class="sxs-lookup"><span data-stu-id="87e46-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="87e46-166">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="87e46-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="87e46-167">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="87e46-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="87e46-168">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="87e46-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87e46-170">按照[使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)中的说明创建名为 GrpcGreeterClient 的控制台应用  。</span><span class="sxs-lookup"><span data-stu-id="87e46-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="87e46-171">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="87e46-171">Add required packages</span></span>

<span data-ttu-id="87e46-172">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="87e46-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="87e46-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="87e46-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="87e46-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="87e46-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="87e46-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="87e46-176">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="87e46-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87e46-178">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="87e46-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="87e46-179">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="87e46-179">PMC option to install packages</span></span>

* <span data-ttu-id="87e46-180">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="87e46-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="87e46-181">从“包管理器控制台”窗口中，运行 `cd GrpcGreeterClient` 以将目录更改为包含 GrpcGreeterClient.csproj 文件的文件夹   。</span><span class="sxs-lookup"><span data-stu-id="87e46-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="87e46-182">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="87e46-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="87e46-183">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="87e46-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="87e46-184">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="87e46-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="87e46-185">选择“浏览”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="87e46-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="87e46-186">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="87e46-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="87e46-187">从“浏览”选项卡中选择“Grpc.Net.Client”包，然后选择“安装”    。</span><span class="sxs-lookup"><span data-stu-id="87e46-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="87e46-188">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="87e46-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="87e46-190">从“集成终端”运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="87e46-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-191">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="87e46-192">右键单击“Solution Pad” > “添加包...”中的“包”文件夹   </span><span class="sxs-lookup"><span data-stu-id="87e46-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="87e46-193">在搜索框中输入 Grpc.Net.Client  。</span><span class="sxs-lookup"><span data-stu-id="87e46-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="87e46-194">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”  </span><span class="sxs-lookup"><span data-stu-id="87e46-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="87e46-195">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="87e46-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="87e46-196">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="87e46-196">Add greet.proto</span></span>

* <span data-ttu-id="87e46-197">在 gRPC 客户端项目中创建 Protos 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="87e46-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="87e46-198">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目  。</span><span class="sxs-lookup"><span data-stu-id="87e46-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="87e46-199">编辑 GrpcGreeterClient.csproj 项目文件  ：</span><span class="sxs-lookup"><span data-stu-id="87e46-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="87e46-201">右键单击项目，并选择“编辑项目文件”  。</span><span class="sxs-lookup"><span data-stu-id="87e46-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="87e46-203">选择 GrpcGreeterClient.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="87e46-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="87e46-205">右键单击项目，并选择“工具” > “编辑文件”   。</span><span class="sxs-lookup"><span data-stu-id="87e46-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="87e46-206">添加具有引用 greet.proto  文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="87e46-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="87e46-207">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="87e46-207">Create the Greeter client</span></span>

<span data-ttu-id="87e46-208">构建项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="87e46-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="87e46-209">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="87e46-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="87e46-210">使用以下代码更新 gRPC 客户端的 Program.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="87e46-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="87e46-211">Program.cs  包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="87e46-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="87e46-212">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="87e46-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="87e46-213">实例化 `GrpcChannel`，使其包含用于创建到 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="87e46-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="87e46-214">使用 `GrpcChannel` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="87e46-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="87e46-215">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="87e46-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="87e46-216">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="87e46-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="87e46-217">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="87e46-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87e46-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87e46-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="87e46-219">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="87e46-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="87e46-220">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="87e46-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87e46-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="87e46-222">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-222">Start the Greeter service.</span></span>
* <span data-ttu-id="87e46-223">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-223">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87e46-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="87e46-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="87e46-225">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-225">Start the Greeter service.</span></span>
* <span data-ttu-id="87e46-226">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="87e46-226">Start the client.</span></span>

---

<span data-ttu-id="87e46-227">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息  。</span><span class="sxs-lookup"><span data-stu-id="87e46-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="87e46-228">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="87e46-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="87e46-229">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="87e46-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="87e46-230">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息：</span><span class="sxs-lookup"><span data-stu-id="87e46-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="87e46-231">本文中的代码需要 ASP.NET Core HTTPS 开发证书来保护 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="87e46-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="87e46-232">如果客户端失败并显示消息 `The remote certificate is invalid according to the validation procedure.`，则开发证书不受信任。</span><span class="sxs-lookup"><span data-stu-id="87e46-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="87e46-233">有关解决此问题的说明，请参阅[在 Windows 和 macOS 上信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="87e46-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="87e46-234">后续步骤</span><span class="sxs-lookup"><span data-stu-id="87e46-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
