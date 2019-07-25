---
title: Azure App Service 和 IIS 上的 ASP.NET Core 疑难解答
author: guardrex
description: 了解如何诊断 ASP.NET Core 应用 Azure App Service 和 Internet Information Services (IIS) 部署的问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/18/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: deae568a6ba88c9a8365b9d7f2df629899bc64a1
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483311"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="81df8-103">Azure App Service 和 IIS 上的 ASP.NET Core 疑难解答</span><span class="sxs-lookup"><span data-stu-id="81df8-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="81df8-104">作者: [Luke Latham](https://github.com/guardrex)和[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="81df8-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="81df8-105">本文提供了有关常见应用启动错误的信息, 以及在将应用部署到 Azure App Service 或 IIS 时如何诊断错误的说明:</span><span class="sxs-lookup"><span data-stu-id="81df8-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="81df8-106">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="81df8-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="81df8-107">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="81df8-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="81df8-108">Azure App Service 疑难解答</span><span class="sxs-lookup"><span data-stu-id="81df8-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="81df8-109">为部署到 Azure App Service 的应用提供故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="81df8-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="81df8-110">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="81df8-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="81df8-111">为部署到 IIS 或本地运行 IIS Express 的应用程序提供故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="81df8-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="81df8-112">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="81df8-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="81df8-113">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="81df8-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="81df8-114">说明当一致包在执行重大升级或更改包版本时中断应用时应执行的操作。</span><span class="sxs-lookup"><span data-stu-id="81df8-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="81df8-115">其他资源</span><span class="sxs-lookup"><span data-stu-id="81df8-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="81df8-116">列出其他故障排除主题。</span><span class="sxs-lookup"><span data-stu-id="81df8-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="81df8-117">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="81df8-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="81df8-118">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="81df8-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="81df8-119">在本地调试时, 可以使用本主题中的建议来诊断 " *502.5-进程故障*" 或 " *500.30-启动" 故障*。</span><span class="sxs-lookup"><span data-stu-id="81df8-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="81df8-120">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="81df8-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="81df8-121">使用本主题中的建议可以诊断本地调试时发生的*502.5 进程故障*。</span><span class="sxs-lookup"><span data-stu-id="81df8-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="81df8-122">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="81df8-122">403.14 Forbidden</span></span>

<span data-ttu-id="81df8-123">应用启动失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-123">The app fails to start.</span></span> <span data-ttu-id="81df8-124">记录以下错误:</span><span class="sxs-lookup"><span data-stu-id="81df8-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="81df8-125">此错误通常是由托管系统上的部署中断引起的, 其中包括以下任何方案:</span><span class="sxs-lookup"><span data-stu-id="81df8-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="81df8-126">该应用程序部署到托管系统上的错误文件夹中。</span><span class="sxs-lookup"><span data-stu-id="81df8-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="81df8-127">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="81df8-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="81df8-128">部署中缺少*web.config*文件, 或*web.config 文件内容*的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="81df8-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="81df8-129">执行以下步骤:</span><span class="sxs-lookup"><span data-stu-id="81df8-129">Perform the following steps:</span></span>

1. <span data-ttu-id="81df8-130">删除宿主系统上的部署文件夹中的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="81df8-131">使用常规部署方法 (如 Visual Studio、PowerShell 或手动部署) 将应用程序的 "*发布*" 文件夹的内容重新部署到宿主系统:</span><span class="sxs-lookup"><span data-stu-id="81df8-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="81df8-132">确认部署中存在*web.config*文件且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="81df8-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="81df8-133">在 Azure App Service 上承载时, 确认该应用程序已部署到`D:\home\site\wwwroot`该文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="81df8-134">当应用程序由 IIS 承载时, 确认该应用程序部署到 iis**管理器**的 "**基本设置**" 中显示的 iis**物理路径**。</span><span class="sxs-lookup"><span data-stu-id="81df8-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="81df8-135">通过将主机系统上的部署与项目的 "*发布*" 文件夹的内容进行比较, 确认应用的所有文件和文件夹都已部署。</span><span class="sxs-lookup"><span data-stu-id="81df8-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="81df8-136">有关已发布 ASP.NET Core 应用布局的详细信息, 请参阅<xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="81df8-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="81df8-137">有关*web.config*文件的详细信息, 请参阅<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="81df8-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="81df8-138">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="81df8-138">500 Internal Server Error</span></span>

<span data-ttu-id="81df8-139">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="81df8-140">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="81df8-141">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="81df8-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="81df8-142">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="81df8-143">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="81df8-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="81df8-144">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="81df8-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="81df8-145">在服务器上的命令提示符处运行应用程序, 或启用 ASP.NET Core Module stdout 日志来解决该问题。</span><span class="sxs-lookup"><span data-stu-id="81df8-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="81df8-146">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="81df8-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="81df8-147">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-147">The worker process fails.</span></span> <span data-ttu-id="81df8-148">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-148">The app doesn't start.</span></span>

<span data-ttu-id="81df8-149">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)未能找到 .NET Core CLR 并找不到进程内请求处理程序 (*aspnetcorev2_inprocess*)。</span><span class="sxs-lookup"><span data-stu-id="81df8-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="81df8-150">检查：</span><span class="sxs-lookup"><span data-stu-id="81df8-150">Check that:</span></span>

* <span data-ttu-id="81df8-151">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="81df8-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="81df8-152">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="81df8-153">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="81df8-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="81df8-154">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-154">The worker process fails.</span></span> <span data-ttu-id="81df8-155">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-155">The app doesn't start.</span></span>

<span data-ttu-id="81df8-156">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)未能找到进程外宿主请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="81df8-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="81df8-157">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll。</span><span class="sxs-lookup"><span data-stu-id="81df8-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="81df8-158">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="81df8-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="81df8-159">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-159">The worker process fails.</span></span> <span data-ttu-id="81df8-160">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-160">The app doesn't start.</span></span>

<span data-ttu-id="81df8-161">加载[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="81df8-162">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-162">Take one of the following actions:</span></span>

* <span data-ttu-id="81df8-163">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”）。</span><span class="sxs-lookup"><span data-stu-id="81df8-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="81df8-164">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="81df8-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="81df8-165">在 [GitHub 存储库](https://github.com/aspnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="81df8-165">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="81df8-166">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="81df8-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="81df8-167">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-167">The worker process fails.</span></span> <span data-ttu-id="81df8-168">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-168">The app doesn't start.</span></span>

<span data-ttu-id="81df8-169">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试在进程中启动 .NET Core CLR, 但无法启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="81df8-170">通常, 可以根据应用程序事件日志中的条目和 ASP.NET Core 模块 stdout 日志中的条目来确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="81df8-171">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="81df8-172">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="81df8-173">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="81df8-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="81df8-174">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-174">The worker process fails.</span></span> <span data-ttu-id="81df8-175">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-175">The app doesn't start.</span></span>

<span data-ttu-id="81df8-176">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试在进程内启动 .net Core 运行时, 但无法启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="81df8-177">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="81df8-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="81df8-178">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="81df8-179">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="81df8-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="81df8-180">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="81df8-181">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="81df8-181">To fix this error, either:</span></span>

* <span data-ttu-id="81df8-182">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="81df8-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="81df8-183">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="81df8-184">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="81df8-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="81df8-185">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="81df8-186">在应用程序事件日志中也可以找到进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="81df8-187">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="81df8-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="81df8-188">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-188">The worker process fails.</span></span> <span data-ttu-id="81df8-189">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-189">The app doesn't start.</span></span>

<span data-ttu-id="81df8-190">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="81df8-191">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="81df8-192">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="81df8-192">To fix this error, either:</span></span>

* <span data-ttu-id="81df8-193">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="81df8-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="81df8-194">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="81df8-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="81df8-195">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="81df8-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="81df8-196">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-196">The worker process fails.</span></span> <span data-ttu-id="81df8-197">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-197">The app doesn't start.</span></span>

<span data-ttu-id="81df8-198">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="81df8-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="81df8-199">只有面向该框架`Microsoft.AspNetCore.App`的应用才能由[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)承载。</span><span class="sxs-lookup"><span data-stu-id="81df8-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="81df8-200">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="81df8-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="81df8-201">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="81df8-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="81df8-202">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="81df8-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="81df8-203">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="81df8-204">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="81df8-205">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="81df8-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="81df8-206">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-206">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="81df8-207">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="81df8-208">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="81df8-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="81df8-209">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻。</span><span class="sxs-lookup"><span data-stu-id="81df8-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="81df8-210">这表示安装的[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)已损坏。</span><span class="sxs-lookup"><span data-stu-id="81df8-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="81df8-211">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="81df8-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="81df8-212">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="81df8-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="81df8-213">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="81df8-214">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="81df8-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="81df8-215">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="81df8-216">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="81df8-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="81df8-217">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="81df8-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="81df8-218">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="81df8-218">502.5 Process Failure</span></span>

<span data-ttu-id="81df8-219">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-219">The worker process fails.</span></span> <span data-ttu-id="81df8-220">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="81df8-220">The app doesn't start.</span></span>

<span data-ttu-id="81df8-221">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="81df8-222">通常, 可以根据应用程序事件日志中的条目和 ASP.NET Core 模块 stdout 日志中的条目来确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="81df8-223">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="81df8-224">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="81df8-225">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="81df8-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="81df8-226">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="81df8-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="81df8-227">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="81df8-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="81df8-228">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="81df8-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="81df8-229">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="81df8-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="81df8-230">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="81df8-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="81df8-231">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="81df8-232">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="81df8-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="81df8-233">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="81df8-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="81df8-234">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="81df8-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="81df8-235">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="81df8-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="81df8-236">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="81df8-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="81df8-237">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="81df8-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="81df8-238">确认项目文件中的`<Platform>` MSBuild 属性和应用的已发布位之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="81df8-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="81df8-239">连接重置</span><span class="sxs-lookup"><span data-stu-id="81df8-239">Connection reset</span></span>

<span data-ttu-id="81df8-240">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="81df8-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="81df8-241">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="81df8-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="81df8-242">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="81df8-243">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="81df8-244">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="81df8-244">Default startup limits</span></span>

<span data-ttu-id="81df8-245">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)配置了默认*startupTimeLimit*为120秒。</span><span class="sxs-lookup"><span data-stu-id="81df8-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="81df8-246">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="81df8-247">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="81df8-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="81df8-248">Azure App Service 疑难解答</span><span class="sxs-lookup"><span data-stu-id="81df8-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="81df8-249">应用程序事件日志 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="81df8-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="81df8-250">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="81df8-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="81df8-251">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="81df8-252">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="81df8-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="81df8-253">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="81df8-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="81df8-254">在“支持工具”下，选择“应用程序事件”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="81df8-255">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="81df8-256">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="81df8-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="81df8-257">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="81df8-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="81df8-258">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-259">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="81df8-260">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="81df8-261">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="81df8-262">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="81df8-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="81df8-263">检查日志。</span><span class="sxs-lookup"><span data-stu-id="81df8-263">Examine the log.</span></span> <span data-ttu-id="81df8-264">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="81df8-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="81df8-265">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="81df8-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="81df8-266">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="81df8-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="81df8-267">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="81df8-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="81df8-268">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="81df8-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="81df8-269">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-270">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="81df8-271">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="81df8-272">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="81df8-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="81df8-273">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="81df8-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="81df8-274">运行应用：</span><span class="sxs-lookup"><span data-stu-id="81df8-274">Run the app:</span></span>
   * <span data-ttu-id="81df8-275">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="81df8-276">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="81df8-277">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="81df8-278">**在预览版本中运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="81df8-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="81df8-279">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="81df8-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="81df8-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="81df8-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="81df8-281">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="81df8-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="81df8-282">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="81df8-283">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="81df8-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="81df8-284">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="81df8-284">**Current release**</span></span>

* <span data-ttu-id="81df8-285">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="81df8-286">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="81df8-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="81df8-287">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="81df8-288">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="81df8-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="81df8-289">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="81df8-290">**在预览版本中运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="81df8-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="81df8-291">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="81df8-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="81df8-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="81df8-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="81df8-293">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="81df8-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="81df8-294">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="81df8-295">ASP.NET Core 模块 stdout 日志 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="81df8-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="81df8-296">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="81df8-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="81df8-297">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="81df8-298">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="81df8-299">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="81df8-300">在“建议的解决方案” > “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="81df8-301">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="81df8-302">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="81df8-303">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="81df8-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="81df8-304">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="81df8-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="81df8-305">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="81df8-306">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-306">Make a request to the app.</span></span>
1. <span data-ttu-id="81df8-307">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="81df8-307">Return to the Azure portal.</span></span> <span data-ttu-id="81df8-308">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="81df8-309">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-310">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="81df8-311">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="81df8-312">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="81df8-313">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="81df8-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="81df8-314">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="81df8-315">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="81df8-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="81df8-316">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="81df8-317">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="81df8-318">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="81df8-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="81df8-319">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="81df8-320">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> 。</span><span class="sxs-lookup"><span data-stu-id="81df8-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="81df8-321">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="81df8-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="81df8-322">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="81df8-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="81df8-323">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="81df8-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="81df8-324">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="81df8-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="81df8-325">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="81df8-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="81df8-326">ASP.NET Core 模块调试日志 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="81df8-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="81df8-327">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="81df8-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="81df8-328">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="81df8-329">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="81df8-330">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="81df8-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="81df8-331">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-331">Redeploy the app.</span></span>
   * <span data-ttu-id="81df8-332">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="81df8-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="81df8-333">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="81df8-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="81df8-334">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-335">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="81df8-336">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="81df8-337">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="81df8-338">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="81df8-339">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="81df8-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="81df8-340">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="81df8-341">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="81df8-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="81df8-342">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-343">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="81df8-344">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="81df8-345">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="81df8-346">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="81df8-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="81df8-347">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="81df8-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="81df8-348">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="81df8-349">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="81df8-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="81df8-350">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="81df8-351">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="81df8-352">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="81df8-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="81df8-353">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-353">Save the file.</span></span>

<span data-ttu-id="81df8-354">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs> 。</span><span class="sxs-lookup"><span data-stu-id="81df8-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="81df8-355">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="81df8-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="81df8-356">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="81df8-356">There's no limit on log file size.</span></span> <span data-ttu-id="81df8-357">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="81df8-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="81df8-358">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="81df8-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="81df8-359">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="81df8-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="81df8-360">缓慢或悬挂式应用 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="81df8-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="81df8-361">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="81df8-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="81df8-362">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="81df8-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="81df8-363">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="81df8-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="81df8-364">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="81df8-364">Monitoring blades</span></span>

<span data-ttu-id="81df8-365">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="81df8-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="81df8-366">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="81df8-367">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="81df8-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="81df8-368">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="81df8-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="81df8-369">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="81df8-370">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="81df8-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="81df8-371">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="81df8-372">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="81df8-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="81df8-373">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="81df8-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="81df8-374">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="81df8-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="81df8-375">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="81df8-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="81df8-376">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="81df8-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="81df8-377">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="81df8-378">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="81df8-379">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="81df8-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="81df8-380">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="81df8-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="81df8-381">打开路径“site > wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="81df8-382">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="81df8-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="81df8-383">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="81df8-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="81df8-384">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="81df8-385">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="81df8-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="81df8-386">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="81df8-387">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="81df8-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="81df8-388">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="81df8-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="81df8-389">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="81df8-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="81df8-390">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="81df8-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="81df8-391">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-391">Make a request to the app.</span></span>
1. <span data-ttu-id="81df8-392">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="81df8-393">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="81df8-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="81df8-394">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="81df8-395">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="81df8-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="81df8-396">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="81df8-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="81df8-397">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="81df8-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="81df8-398">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="81df8-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="81df8-399">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="81df8-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="81df8-400">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="81df8-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="81df8-401">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="81df8-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="81df8-402">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="81df8-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="81df8-403">IIS 故障排除</span><span class="sxs-lookup"><span data-stu-id="81df8-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="81df8-404">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="81df8-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="81df8-405">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="81df8-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="81df8-406">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="81df8-407">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="81df8-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="81df8-408">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="81df8-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="81df8-409">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="81df8-410">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="81df8-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="81df8-411">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="81df8-411">Run the app at a command prompt</span></span>

<span data-ttu-id="81df8-412">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="81df8-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="81df8-413">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="81df8-414">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="81df8-414">Framework-dependent deployment</span></span>

<span data-ttu-id="81df8-415">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="81df8-416">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="81df8-417">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="81df8-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="81df8-418">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="81df8-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="81df8-419">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="81df8-420">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="81df8-421">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="81df8-422">独立部署</span><span class="sxs-lookup"><span data-stu-id="81df8-422">Self-contained deployment</span></span>

<span data-ttu-id="81df8-423">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="81df8-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="81df8-424">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="81df8-425">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="81df8-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="81df8-426">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="81df8-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="81df8-427">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="81df8-428">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="81df8-429">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="81df8-430">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="81df8-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="81df8-431">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="81df8-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="81df8-432">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="81df8-433">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="81df8-434">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="81df8-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="81df8-435">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-435">Edit the *web.config* file.</span></span> <span data-ttu-id="81df8-436">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="81df8-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="81df8-437">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="81df8-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="81df8-438">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="81df8-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="81df8-439">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="81df8-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="81df8-440">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="81df8-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="81df8-441">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="81df8-442">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="81df8-442">Make a request to the app.</span></span>
1. <span data-ttu-id="81df8-443">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="81df8-444">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="81df8-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="81df8-445">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="81df8-445">Study the log for errors.</span></span>

<span data-ttu-id="81df8-446">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="81df8-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="81df8-447">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="81df8-448">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="81df8-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="81df8-449">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-449">Save the file.</span></span>

<span data-ttu-id="81df8-450">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="81df8-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="81df8-451">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="81df8-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="81df8-452">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="81df8-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="81df8-453">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="81df8-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="81df8-454">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="81df8-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="81df8-455">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="81df8-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="81df8-456">将以下处理程序设置添加到应用程序的*web.config 文件,* 以启用 ASP.NET Core 模块调试日志:</span><span class="sxs-lookup"><span data-stu-id="81df8-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="81df8-457">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="81df8-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="81df8-458">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs> 。</span><span class="sxs-lookup"><span data-stu-id="81df8-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="81df8-459">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="81df8-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="81df8-460">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="81df8-461">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="81df8-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="81df8-462">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="81df8-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="81df8-463">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="81df8-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="81df8-464">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="81df8-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="81df8-465">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="81df8-465">Obtain data from an app</span></span>

<span data-ttu-id="81df8-466">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="81df8-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="81df8-467">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="81df8-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="81df8-468">慢或挂起应用 (IIS)</span><span class="sxs-lookup"><span data-stu-id="81df8-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="81df8-469">*故障转储*是系统内存的快照, 可帮助确定应用崩溃、启动失败或应用速度缓慢的原因。</span><span class="sxs-lookup"><span data-stu-id="81df8-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="81df8-470">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="81df8-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="81df8-471">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="81df8-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="81df8-472">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="81df8-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="81df8-473">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="81df8-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="81df8-474">运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="81df8-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="81df8-475">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="81df8-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="81df8-476">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="81df8-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="81df8-477">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="81df8-478">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="81df8-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="81df8-479">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="81df8-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="81df8-480">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="81df8-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="81df8-481">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="81df8-482">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="81df8-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="81df8-483">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="81df8-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="81df8-484">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="81df8-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="81df8-485">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="81df8-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="81df8-486">分析转储</span><span class="sxs-lookup"><span data-stu-id="81df8-486">Analyze the dump</span></span>

<span data-ttu-id="81df8-487">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="81df8-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="81df8-488">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="81df8-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="81df8-489">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="81df8-489">Clear package caches</span></span>

<span data-ttu-id="81df8-490">有时, 在升级开发计算机上的 .NET Core SDK 或更改应用中的包版本后, 运行中的应用会立即失败。</span><span class="sxs-lookup"><span data-stu-id="81df8-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="81df8-491">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="81df8-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="81df8-492">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="81df8-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="81df8-493">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="81df8-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="81df8-494">通过从命令行界面执行`dotnet nuget locals all --clear`来清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="81df8-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="81df8-495">还可以通过[nuget.exe](https://www.nuget.org/downloads)工具和执行命令`nuget locals all -clear`来清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="81df8-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="81df8-496">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="81df8-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="81df8-497">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="81df8-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="81df8-498">重新部署应用之前, 请先删除服务器上部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="81df8-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81df8-499">其他资源</span><span class="sxs-lookup"><span data-stu-id="81df8-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="81df8-500">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="81df8-500">Azure documentation</span></span>

* [<span data-ttu-id="81df8-501">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="81df8-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="81df8-502">使用 Visual Studio 对 Azure App Service 中的 web 应用进行故障排除的远程调试 web 应用部分</span><span class="sxs-lookup"><span data-stu-id="81df8-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="81df8-503">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="81df8-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="81df8-504">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="81df8-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="81df8-505">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="81df8-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="81df8-506">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="81df8-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="81df8-507">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="81df8-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="81df8-508">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="81df8-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="81df8-509">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="81df8-509">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="81df8-510">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="81df8-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="81df8-511">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="81df8-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="81df8-512">Visual Studio 2017 中 IIS 上的远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81df8-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="81df8-513">Visual Studio 2017 中远程调试 ASP.NET Core 在远程 IIS 计算机上</span><span class="sxs-lookup"><span data-stu-id="81df8-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="81df8-514">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="81df8-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="81df8-515">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="81df8-515">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="81df8-516">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="81df8-516">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
