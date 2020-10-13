---
title: 托管捆绑包
author: rick-anderson
description: 了解如何配置 .NET Core 托管捆绑包。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755144"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="213bb-103">.NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="213bb-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="213bb-104">.NET Core 托管捆绑包是 .NET Core 运行时和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装程序。</span><span class="sxs-lookup"><span data-stu-id="213bb-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="213bb-105">该捆绑包允许 ASP.NET Core 应用通过 IIS 运行。</span><span class="sxs-lookup"><span data-stu-id="213bb-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="213bb-106">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="213bb-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="213bb-107">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="213bb-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="213bb-108">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="213bb-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="213bb-109">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="213bb-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="213bb-110">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="213bb-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="213bb-111">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="213bb-111">Direct download (current version)</span></span>

<span data-ttu-id="213bb-112">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="213bb-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="213bb-113">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="213bb-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="213bb-114">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="213bb-114">Earlier versions of the installer</span></span>

<span data-ttu-id="213bb-115">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="213bb-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="213bb-116">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="213bb-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="213bb-117">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="213bb-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="213bb-118">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="213bb-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="213bb-119">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="213bb-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="213bb-120">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="213bb-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="213bb-121">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="213bb-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="213bb-122">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="213bb-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="213bb-123">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="213bb-123">Run the installer on the server.</span></span> <span data-ttu-id="213bb-124">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="213bb-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="213bb-125">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="213bb-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="213bb-126">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="213bb-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="213bb-127">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="213bb-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="213bb-128">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="213bb-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="213bb-129">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="213bb-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="213bb-130">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="213bb-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="213bb-131">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="213bb-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="213bb-132">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="213bb-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="213bb-133">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (`applicationHost.config`) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="213bb-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="213bb-134">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="213bb-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="213bb-135">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="213bb-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="213bb-136">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="213bb-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="213bb-137">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="213bb-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="213bb-138">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="213bb-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="213bb-139">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="213bb-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="213bb-140">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="213bb-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="213bb-141">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="213bb-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="213bb-142">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="213bb-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="213bb-143">在托管系统上，导航到 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`。</span><span class="sxs-lookup"><span data-stu-id="213bb-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="213bb-144">找到 `aspnetcorev2.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="213bb-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="213bb-145">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="213bb-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="213bb-146">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="213bb-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="213bb-147">可在 `C:\Users\%UserName%\AppData\Local\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="213bb-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="213bb-148">该文件的名称为 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`，其中占位符 `{TIMESTAMP}` 为文件的时间戳。</span><span class="sxs-lookup"><span data-stu-id="213bb-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
