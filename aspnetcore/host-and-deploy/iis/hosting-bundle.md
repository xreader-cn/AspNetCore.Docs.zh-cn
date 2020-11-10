---
title: 托管捆绑包
author: rick-anderson
description: 了解如何配置 .NET Core 托管捆绑包。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343632"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="44547-103">.NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="44547-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="44547-104">.NET Core 托管捆绑包是 .NET Core 运行时和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装程序。</span><span class="sxs-lookup"><span data-stu-id="44547-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="44547-105">该捆绑包允许 ASP.NET Core 应用通过 IIS 运行。</span><span class="sxs-lookup"><span data-stu-id="44547-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="44547-106">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="44547-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44547-107">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="44547-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="44547-108">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="44547-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="44547-109">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="44547-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="44547-110">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="44547-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="44547-111">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="44547-111">Direct download (current version)</span></span>

<span data-ttu-id="44547-112">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="44547-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="44547-113">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="44547-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="44547-114">Visual C++ 可再发行程序包要求</span><span class="sxs-lookup"><span data-stu-id="44547-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="44547-115">在较旧版本的 Windows（例如 Windows Server 2012 R2）上，安装 Visual Studio C++ 2015、2017、2019 可再发行程序包。</span><span class="sxs-lookup"><span data-stu-id="44547-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="44547-116">否则，Windows 事件日志中令人困惑的错误消息会报告 `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="44547-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="44547-117">[当前 x64 VS C++ 可再发行程序包](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[当前 x86 VS C++ 可再发行程序包](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="44547-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="44547-118">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="44547-118">Earlier versions of the installer</span></span>

<span data-ttu-id="44547-119">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="44547-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="44547-120">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="44547-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="44547-121">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="44547-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="44547-122">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="44547-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="44547-123">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="44547-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="44547-124">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="44547-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="44547-125">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="44547-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="44547-126">选项</span><span class="sxs-lookup"><span data-stu-id="44547-126">Options</span></span>

1. <span data-ttu-id="44547-127">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="44547-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="44547-128">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="44547-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="44547-129">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="44547-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="44547-130">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="44547-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="44547-131">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="44547-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="44547-132">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="44547-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="44547-133">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="44547-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="44547-134">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="44547-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="44547-135">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="44547-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="44547-136">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (`applicationHost.config`) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="44547-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="44547-137">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="44547-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="44547-138">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="44547-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="44547-139">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="44547-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="44547-140">重新启动 IIS</span><span class="sxs-lookup"><span data-stu-id="44547-140">Restart IIS</span></span>

<span data-ttu-id="44547-141">安装托管捆绑包后，可能需要手动重新启动 IIS。</span><span class="sxs-lookup"><span data-stu-id="44547-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="44547-142">例如，在运行 IIS 工作进程的路径上可能不存在 `dotnet` CLI 工具（命令）。</span><span class="sxs-lookup"><span data-stu-id="44547-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="44547-143">若要手动停止和启动 IIS，请在提升的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="44547-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="44547-144">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="44547-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="44547-145">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="44547-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="44547-146">在托管系统上，导航到 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`。</span><span class="sxs-lookup"><span data-stu-id="44547-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="44547-147">找到 `aspnetcorev2.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="44547-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="44547-148">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="44547-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="44547-149">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="44547-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="44547-150">可在 `C:\Users\%UserName%\AppData\Local\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="44547-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="44547-151">该文件的名称为 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`，其中占位符 `{TIMESTAMP}` 为文件的时间戳。</span><span class="sxs-lookup"><span data-stu-id="44547-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
