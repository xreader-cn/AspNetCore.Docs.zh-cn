---
title: 在 ASP.NET Core 中创建 .NET Core gRPC 客户端和服务器
author: juntaoluo
description: 本教程演示了如何在 ASP.NET Core 中创建 gRPC 服务和 gRPC 客户端。 了解如何创建 gRPC 服务项目、编辑原型文件并添加双工流式处理调用。
ms.author: johluo
ms.date: 10/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057019"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="59fef-104">教程：在 ASP.NET Core 中创建 gRPC 客户端和服务器</span><span class="sxs-lookup"><span data-stu-id="59fef-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="59fef-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="59fef-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="59fef-106">本教程演示了如何创建 .NET Core [gRPC](https://grpc.io/docs/guides/) 客户端和 ASP.NET Core gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="59fef-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="59fef-107">最后会生成与 gRPC Greeter 服务进行通信的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="59fef-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="59fef-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="59fef-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="59fef-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="59fef-110">创建 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="59fef-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="59fef-111">创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="59fef-112">使用 gRPC Greeter 服务测试 gRPC 客户端服务。</span><span class="sxs-lookup"><span data-stu-id="59fef-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="59fef-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="59fef-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59fef-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="59fef-117">Visual Studio for Mac 8.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="59fef-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="59fef-118">创建 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="59fef-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59fef-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59fef-120">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="59fef-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="59fef-121">或者，从 Visual Studio“文件”菜单中选择“新建” > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="59fef-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="59fef-122">在“创建新项目”对话框中，选择“gRPC 服务”，然后选择“下一步”  ：</span><span class="sxs-lookup"><span data-stu-id="59fef-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Visual Studio 中的“创建新项目”对话框](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="59fef-124">将项目命名为 GrpcGreeter。</span><span class="sxs-lookup"><span data-stu-id="59fef-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="59fef-125">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="59fef-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="59fef-126">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="59fef-126">Select **Create**.</span></span>
* <span data-ttu-id="59fef-127">在“创建新 gRPC 服务”对话框中：</span><span class="sxs-lookup"><span data-stu-id="59fef-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="59fef-128">选择“gRPC 服务”模板。</span><span class="sxs-lookup"><span data-stu-id="59fef-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="59fef-129">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="59fef-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="59fef-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="59fef-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="59fef-132">将目录 (`cd`) 更改为项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="59fef-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="59fef-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59fef-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="59fef-134">`dotnet new` 命令将在 GrpcGreeter 文件夹中创建一个新 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="59fef-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="59fef-135">`code` 命令将在新 Visual Studio Code 实例中打开 GrpcGreeter 文件夹。</span><span class="sxs-lookup"><span data-stu-id="59fef-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="59fef-136">一个对话框随即出现，其中包含：“‘GrpcGreeter’中缺少进行生成和调试所需的资产”。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="59fef-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="59fef-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="59fef-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="59fef-139">启动 Visual Studio for Mac 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="59fef-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="59fef-140">或者，从 Visual Studio“文件”菜单中选择“新建” > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="59fef-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="59fef-141">在“创建新项目”对话框中，选择“Web 和控制台” > “应用” > “gRPC 服务”，然后选择“下一步”    ：</span><span class="sxs-lookup"><span data-stu-id="59fef-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![macOS 上的“创建新项目”对话框](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="59fef-143">选择“.NET Core 3.1”作为“目标框架”，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="59fef-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="59fef-144">将项目命名为 GrpcGreeter。</span><span class="sxs-lookup"><span data-stu-id="59fef-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="59fef-145">将项目命名为“GrpcGreeter”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="59fef-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="59fef-146">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="59fef-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="59fef-147">运行服务</span><span class="sxs-lookup"><span data-stu-id="59fef-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="59fef-148">日志显示该服务正在侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="59fef-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="59fef-149">gRPC 模板配置为使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。</span><span class="sxs-lookup"><span data-stu-id="59fef-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="59fef-150">gRPC 客户端需要使用 HTTPS 调用服务器。</span><span class="sxs-lookup"><span data-stu-id="59fef-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="59fef-151">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="59fef-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="59fef-152">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="59fef-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="59fef-153">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="59fef-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="59fef-154">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="59fef-154">Examine the project files</span></span>

<span data-ttu-id="59fef-155">GrpcGreeter 项目文件：</span><span class="sxs-lookup"><span data-stu-id="59fef-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="59fef-156">*greet.proto* ： *Protos/greet.proto* 文件定义 `Greeter` gRPC，且用于生成 gRPC 服务器资产。</span><span class="sxs-lookup"><span data-stu-id="59fef-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="59fef-157">有关详细信息，请参阅 [gRPC 介绍](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="59fef-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="59fef-158">Services 文件夹：包含 `Greeter` 服务的实现。</span><span class="sxs-lookup"><span data-stu-id="59fef-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="59fef-159">*appSettings.json* ：包含配置数据，例如 Kestrel 使用的协议。</span><span class="sxs-lookup"><span data-stu-id="59fef-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="59fef-160">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="59fef-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="59fef-161">Program.cs:包含 gRPC 服务的入口点。</span><span class="sxs-lookup"><span data-stu-id="59fef-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="59fef-162">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="59fef-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="59fef-163">*Startup.cs* ：包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="59fef-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="59fef-164">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="59fef-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="59fef-165">在 .NET 控制台应用中创建 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="59fef-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59fef-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59fef-167">打开 Visual Studio 的第二个实例并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="59fef-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="59fef-168">在“创建新项目”对话框中，选择“控制台应用(.NET Core)”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="59fef-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="59fef-169">在“项目名称”文本框中，输入“GrpcGreeterClient”，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="59fef-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="59fef-171">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="59fef-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="59fef-172">将目录 (`cd`) 更改为项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="59fef-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="59fef-173">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59fef-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="59fef-175">按照[使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)中的说明创建名为 GrpcGreeterClient 的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="59fef-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="59fef-176">添加所需的包</span><span class="sxs-lookup"><span data-stu-id="59fef-176">Add required packages</span></span>

<span data-ttu-id="59fef-177">gRPC 客户端项目需要以下包：</span><span class="sxs-lookup"><span data-stu-id="59fef-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="59fef-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，其中包含 .NET Core 客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="59fef-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 包含适用于 C# 的 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="59fef-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="59fef-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 包含适用于 Protobuf 文件的 C# 工具支持。</span><span class="sxs-lookup"><span data-stu-id="59fef-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="59fef-181">运行时不需要工具包，因此依赖项标记为 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="59fef-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59fef-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59fef-183">通过包管理器控制台 (PMC) 或管理 NuGet 包来安装包。</span><span class="sxs-lookup"><span data-stu-id="59fef-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="59fef-184">用于安装包的 PMC 选项</span><span class="sxs-lookup"><span data-stu-id="59fef-184">PMC option to install packages</span></span>

* <span data-ttu-id="59fef-185">从 Visual Studio 中，依次选择“工具” > “NuGet 包管理器” > “包管理器控制台”  </span><span class="sxs-lookup"><span data-stu-id="59fef-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="59fef-186">从“包管理器控制台”窗口中，运行 `cd GrpcGreeterClient` 以将目录更改为包含 GrpcGreeterClient.csproj 文件的文件夹。</span><span class="sxs-lookup"><span data-stu-id="59fef-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="59fef-187">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59fef-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="59fef-188">管理 NuGet 包选项以安装包</span><span class="sxs-lookup"><span data-stu-id="59fef-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="59fef-189">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目 。</span><span class="sxs-lookup"><span data-stu-id="59fef-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="59fef-190">选择“浏览”选项卡。</span><span class="sxs-lookup"><span data-stu-id="59fef-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="59fef-191">在搜索框中输入 Grpc.Net.Client。</span><span class="sxs-lookup"><span data-stu-id="59fef-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="59fef-192">从“浏览”选项卡中选择“Grpc.Net.Client”包，然后选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="59fef-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="59fef-193">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="59fef-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59fef-195">从“集成终端”运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59fef-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="59fef-197">在 Solution Pad 中，右键单击 GrpcGreeterClient 项目，然后选择“管理 NuGet 包”。</span><span class="sxs-lookup"><span data-stu-id="59fef-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="59fef-198">在搜索框中输入 Grpc.Net.Client。</span><span class="sxs-lookup"><span data-stu-id="59fef-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="59fef-199">从结果窗格中选择 Grpc.Net.Client 包并选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="59fef-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="59fef-200">在“接受许可证”对话框中，选择“接受”按钮。</span><span class="sxs-lookup"><span data-stu-id="59fef-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="59fef-201">为 `Google.Protobuf` 和 `Grpc.Tools` 重复这些步骤。</span><span class="sxs-lookup"><span data-stu-id="59fef-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="59fef-202">添加 greet.proto</span><span class="sxs-lookup"><span data-stu-id="59fef-202">Add greet.proto</span></span>

* <span data-ttu-id="59fef-203">在 gRPC 客户端项目中创建 Protos 文件夹。</span><span class="sxs-lookup"><span data-stu-id="59fef-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="59fef-204">从 gRPC Greeter 服务将 Protos\greet.proto 文件复制到 gRPC 客户端项目。</span><span class="sxs-lookup"><span data-stu-id="59fef-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="59fef-205">将 `greet.proto` 文件中的命名空间更新为项目的命名空间：</span><span class="sxs-lookup"><span data-stu-id="59fef-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="59fef-206">编辑 GrpcGreeterClient.csproj 项目文件：</span><span class="sxs-lookup"><span data-stu-id="59fef-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="59fef-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="59fef-208">右键单击项目，并选择“编辑项目文件”。</span><span class="sxs-lookup"><span data-stu-id="59fef-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="59fef-210">选择 GrpcGreeterClient.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="59fef-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="59fef-212">右键单击项目，并选择“编辑项目文件”。</span><span class="sxs-lookup"><span data-stu-id="59fef-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="59fef-213">添加具有引用 greet.proto 文件的 `<Protobuf>` 元素的项组：</span><span class="sxs-lookup"><span data-stu-id="59fef-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="59fef-214">创建 Greeter 客户端</span><span class="sxs-lookup"><span data-stu-id="59fef-214">Create the Greeter client</span></span>

<span data-ttu-id="59fef-215">构建客户端项目，以在 `GrpcGreeter` 命名空间中创建类型。</span><span class="sxs-lookup"><span data-stu-id="59fef-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="59fef-216">`GrpcGreeter` 类型是由生成进程自动生成的。</span><span class="sxs-lookup"><span data-stu-id="59fef-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="59fef-217">使用以下代码更新 gRPC 客户端的 Program.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="59fef-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="59fef-218">Program.cs 包含 gRPC 客户端的入口点和逻辑。</span><span class="sxs-lookup"><span data-stu-id="59fef-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="59fef-219">通过以下方式创建 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="59fef-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="59fef-220">实例化 `GrpcChannel`，使其包含用于创建到 gRPC 服务的连接的信息。</span><span class="sxs-lookup"><span data-stu-id="59fef-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="59fef-221">使用 `GrpcChannel` 构造 Greeter 客户端：</span><span class="sxs-lookup"><span data-stu-id="59fef-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="59fef-222">Greeter 客户端会调用异步 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="59fef-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="59fef-223">随即显示 `SayHello` 调用的结果：</span><span class="sxs-lookup"><span data-stu-id="59fef-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="59fef-224">使用 gRPC Greeter 服务测试 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="59fef-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59fef-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59fef-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59fef-226">在 Greeter 服务中，按 `Ctrl+F5` 在不使用调试程序的情况下启动服务器。</span><span class="sxs-lookup"><span data-stu-id="59fef-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="59fef-227">在 `GrpcGreeterClient` 项目中，按 `Ctrl+F5` 在不使用调试程序的情况下启动客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59fef-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59fef-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="59fef-229">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="59fef-229">Start the Greeter service.</span></span>
* <span data-ttu-id="59fef-230">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59fef-231">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="59fef-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="59fef-232">根据前面的 [macOS 上 HTTP/2 TLS 问题的变通方法](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)所述，需要将客户端中的通道地址更新为“http://localhost:5000”。</span><span class="sxs-lookup"><span data-stu-id="59fef-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="59fef-233">更新 GrpcGreeterClient/Program.cs 的第 13 行以读取：</span><span class="sxs-lookup"><span data-stu-id="59fef-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="59fef-234">启动 Greeter 服务。</span><span class="sxs-lookup"><span data-stu-id="59fef-234">Start the Greeter service.</span></span>
* <span data-ttu-id="59fef-235">启动客户端。</span><span class="sxs-lookup"><span data-stu-id="59fef-235">Start the client.</span></span>

---

<span data-ttu-id="59fef-236">客户端向该服务发送一条包含具有其名称“GreeterClient”的消息的问候信息。</span><span class="sxs-lookup"><span data-stu-id="59fef-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="59fef-237">该服务会发送“Hello GreeterClient”消息作为答复。</span><span class="sxs-lookup"><span data-stu-id="59fef-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="59fef-238">“Hello GreeterClient”答复将在命令提示符中显示：</span><span class="sxs-lookup"><span data-stu-id="59fef-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="59fef-239">gRPC 服务在写入命令提示符的日志中记录成功调用的详细信息：</span><span class="sxs-lookup"><span data-stu-id="59fef-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="59fef-240">本文中的代码需要 ASP.NET Core HTTPS 开发证书来保护 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="59fef-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="59fef-241">如果 .NET gRPC 客户端失败并显示消息 `The remote certificate is invalid according to the validation procedure.` 或 `The SSL connection could not be established.`，则开发证书不受信任。</span><span class="sxs-lookup"><span data-stu-id="59fef-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="59fef-242">要解决此问题，请参阅[使用不受信任/无效的证书调用 gRPC 服务](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)。</span><span class="sxs-lookup"><span data-stu-id="59fef-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="59fef-243">后续步骤</span><span class="sxs-lookup"><span data-stu-id="59fef-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
