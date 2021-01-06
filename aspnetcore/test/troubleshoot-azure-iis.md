---
title: 对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除
author: rick-anderson
description: 了解如何诊断Azure 应用服务和 Internet Information Services (IIS) 的 ASP.NET Core 应用部署问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: d51a4a43f585b0a0b7e3aab2c5de1b2d215de494
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059593"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="fcdf3-103">对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="fcdf3-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="fcdf3-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fcdf3-105">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fcdf3-106">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fcdf3-107">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fcdf3-108">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fcdf3-109">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fcdf3-110">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fcdf3-111">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fcdf3-112">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fcdf3-113">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fcdf3-114">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fcdf3-115">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fcdf3-116">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fcdf3-117">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-117">App startup errors</span></span>

<span data-ttu-id="fcdf3-118">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fcdf3-119">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fcdf3-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fcdf3-120">403.14 Forbidden</span></span>

<span data-ttu-id="fcdf3-121">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-121">The app fails to start.</span></span> <span data-ttu-id="fcdf3-122">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fcdf3-123">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fcdf3-124">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-125">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-126">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fcdf3-127">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-127">Perform the following steps:</span></span>

1. <span data-ttu-id="fcdf3-128">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-129">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fcdf3-130">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fcdf3-131">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fcdf3-132">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fcdf3-133">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fcdf3-134">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fcdf3-135">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fcdf3-136">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-136">500 Internal Server Error</span></span>

<span data-ttu-id="fcdf3-137">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fcdf3-138">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fcdf3-139">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fcdf3-140">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fcdf3-141">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fcdf3-142">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fcdf3-143">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fcdf3-144">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fcdf3-145">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-145">The worker process fails.</span></span> <span data-ttu-id="fcdf3-146">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-146">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-147">加载 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="fcdf3-148">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-148">Take one of the following actions:</span></span>

* <span data-ttu-id="fcdf3-149">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”） 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="fcdf3-150">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="fcdf3-151">在 [GitHub 存储库](https://github.com/dotnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="fcdf3-152">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="fcdf3-153">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-153">The worker process fails.</span></span> <span data-ttu-id="fcdf3-154">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-154">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-155">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="fcdf3-156">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fcdf3-157">常见失败情况：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-157">Common failure conditions:</span></span>

* <span data-ttu-id="fcdf3-158">由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fcdf3-159">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="fcdf3-160">使用 Azure Key Vault 时，缺少对 Key Vault 的权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="fcdf3-161">检查目标 Key Vault 中的访问策略，确保授予了正确的权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="fcdf3-162">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="fcdf3-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="fcdf3-163">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-163">The worker process fails.</span></span> <span data-ttu-id="fcdf3-164">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-164">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-165">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="fcdf3-166">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="fcdf3-167">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="fcdf3-168">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="fcdf3-169">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="fcdf3-170">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-170">To fix this error, either:</span></span>

* <span data-ttu-id="fcdf3-171">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="fcdf3-172">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="fcdf3-173">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="fcdf3-174">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="fcdf3-175">进程启动失败的原因也可在“应用程序事件日志”中找到。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="fcdf3-176">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="fcdf3-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="fcdf3-177">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-177">The worker process fails.</span></span> <span data-ttu-id="fcdf3-178">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-178">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-179">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="fcdf3-180">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="fcdf3-181">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-181">To fix this error, either:</span></span>

* <span data-ttu-id="fcdf3-182">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="fcdf3-183">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="fcdf3-184">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="fcdf3-185">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-185">The worker process fails.</span></span> <span data-ttu-id="fcdf3-186">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-186">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-187">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fcdf3-188">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="fcdf3-189">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fcdf3-190">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="fcdf3-191">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="fcdf3-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="fcdf3-192">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="fcdf3-193">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="fcdf3-194">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="fcdf3-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="fcdf3-195">工作进程不能在同一进程中运行多个进程内应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="fcdf3-196">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="fcdf3-197">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fcdf3-198">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="fcdf3-199">这表示 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="fcdf3-200">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="fcdf3-201">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="fcdf3-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="fcdf3-202">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="fcdf3-203">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="fcdf3-204">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="fcdf3-205">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="fcdf3-206">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="fcdf3-207">500.38 ANCM 找不到应用程序 DLL</span><span class="sxs-lookup"><span data-stu-id="fcdf3-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="fcdf3-208">ANCM 找不到应用程序 ANCM，该内容应显示在可执行文件的旁边。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="fcdf3-209">在使用进程内托管模型托管打包为[单文件可执行程序](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)的应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="fcdf3-210">该进程内模型要求 ANCM 将 .NET Core 应用加载到现有 IIS 进程中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="fcdf3-211">单文件部署模型不支持此方案。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="fcdf3-212">请在应用的项目文件中使用下述方法之一来修复此错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="fcdf3-213">通过将 `PublishSingleFile` MSBuild 属性设置为 `false` 来禁用单文件发布。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="fcdf3-214">通过将 `AspNetCoreHostingModel` MSBuild 属性设置为 `OutOfProcess` 来切换到进程外托管模型。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fcdf3-215">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-215">502.5 Process Failure</span></span>

<span data-ttu-id="fcdf3-216">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-216">The worker process fails.</span></span> <span data-ttu-id="fcdf3-217">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-217">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-218">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fcdf3-219">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fcdf3-220">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fcdf3-221">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fcdf3-222">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fcdf3-223">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fcdf3-224">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fcdf3-225">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fcdf3-226">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fcdf3-227">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fcdf3-228">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fcdf3-229">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fcdf3-230">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fcdf3-231">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fcdf3-232">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fcdf3-233">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fcdf3-234">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fcdf3-235">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fcdf3-236">连接重置</span><span class="sxs-lookup"><span data-stu-id="fcdf3-236">Connection reset</span></span>

<span data-ttu-id="fcdf3-237">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fcdf3-238">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fcdf3-239">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fcdf3-240">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fcdf3-241">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="fcdf3-241">Default startup limits</span></span>

<span data-ttu-id="fcdf3-242">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fcdf3-243">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fcdf3-244">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fcdf3-245">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fcdf3-246">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-247">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fcdf3-248">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fcdf3-249">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fcdf3-250">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fcdf3-251">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fcdf3-252">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fcdf3-253">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fcdf3-254">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-255">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-256">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-257">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-258">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fcdf3-259">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fcdf3-260">检查日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-260">Examine the log.</span></span> <span data-ttu-id="fcdf3-261">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fcdf3-262">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="fcdf3-263">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-264">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fcdf3-265">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-266">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-267">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-268">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fcdf3-269">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fcdf3-270">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fcdf3-271">运行应用：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-271">Run the app:</span></span>
   * <span data-ttu-id="fcdf3-272">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fcdf3-273">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fcdf3-274">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-275">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-276">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-278">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-279">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fcdf3-280">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fcdf3-281">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-281">**Current release**</span></span>

* <span data-ttu-id="fcdf3-282">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fcdf3-283">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fcdf3-284">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fcdf3-285">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fcdf3-286">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-287">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-288">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-290">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-291">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fcdf3-292">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-293">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-294">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fcdf3-295">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fcdf3-296">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-297">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="fcdf3-298">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-299">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-300">在 Azure 门户中，导航到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="fcdf3-301">在“应用服务”边栏选项卡中，在搜索框中输入 kudu 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="fcdf3-302">依次选择“高级工具”>“Go” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="fcdf3-303">选择“调试控制台”>“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="fcdf3-304">导航到 site/wwwroot</span><span class="sxs-lookup"><span data-stu-id="fcdf3-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="fcdf3-305">选择铅笔图标以编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-306">在 `<aspNetCore />` 元素中，设置 `stdoutLogEnabled="true"` 并选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="fcdf3-307">故障排除完成后，通过设置 `stdoutLogEnabled="false"` 来禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="fcdf3-308">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="fcdf3-309">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-310">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="fcdf3-311">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-312">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="fcdf3-313">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="fcdf3-314">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-314">Redeploy the app.</span></span>
   * <span data-ttu-id="fcdf3-315">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="fcdf3-316">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-317">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-318">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="fcdf3-319">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="fcdf3-320">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-321">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="fcdf3-322">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="fcdf3-323">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="fcdf3-324">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-325">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-326">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-327">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-328">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-329">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="fcdf3-330">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="fcdf3-331">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="fcdf3-332">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="fcdf3-333">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="fcdf3-334">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="fcdf3-335">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="fcdf3-336">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-336">Save the file.</span></span>

<span data-ttu-id="fcdf3-337">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-338">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-339">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-339">There's no limit on log file size.</span></span> <span data-ttu-id="fcdf3-340">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fcdf3-341">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-342">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fcdf3-343">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-344">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fcdf3-345">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-346">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="fcdf3-347">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="fcdf3-347">Monitoring blades</span></span>

<span data-ttu-id="fcdf3-348">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fcdf3-349">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fcdf3-350">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fcdf3-351">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fcdf3-352">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fcdf3-353">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fcdf3-354">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fcdf3-355">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fcdf3-356">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fcdf3-357">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fcdf3-358">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fcdf3-359">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fcdf3-360">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fcdf3-361">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-362">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-363">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-364">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fcdf3-365">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-366">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fcdf3-367">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fcdf3-368">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fcdf3-369">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fcdf3-370">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fcdf3-371">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fcdf3-372">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fcdf3-373">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fcdf3-374">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-374">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-375">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fcdf3-376">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fcdf3-377">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fcdf3-378">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fcdf3-379">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fcdf3-380">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fcdf3-381">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-382">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-383">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-384">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-385">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fcdf3-386">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fcdf3-387">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="fcdf3-388">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fcdf3-389">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fcdf3-390">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fcdf3-391">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fcdf3-392">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fcdf3-393">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fcdf3-394">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-394">Run the app at a command prompt</span></span>

<span data-ttu-id="fcdf3-395">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-396">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fcdf3-397">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-397">Framework-dependent deployment</span></span>

<span data-ttu-id="fcdf3-398">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fcdf3-399">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fcdf3-400">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fcdf3-401">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-402">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-403">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-404">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fcdf3-405">独立部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-405">Self-contained deployment</span></span>

<span data-ttu-id="fcdf3-406">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fcdf3-407">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fcdf3-408">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fcdf3-409">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-410">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-411">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-412">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fcdf3-413">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fcdf3-414">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-415">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-416">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fcdf3-417">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fcdf3-418">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-418">Edit the *web.config* file.</span></span> <span data-ttu-id="fcdf3-419">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fcdf3-420">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fcdf3-421">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fcdf3-422">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fcdf3-423">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fcdf3-424">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-425">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-425">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-426">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fcdf3-427">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fcdf3-428">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-428">Study the log for errors.</span></span>

<span data-ttu-id="fcdf3-429">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fcdf3-430">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-431">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fcdf3-432">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-432">Save the file.</span></span>

<span data-ttu-id="fcdf3-433">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-434">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-435">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-436">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-437">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="fcdf3-438">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="fcdf3-439">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fcdf3-440">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="fcdf3-441">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fcdf3-442">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="fcdf3-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fcdf3-443">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fcdf3-444">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="fcdf3-445">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fcdf3-446">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fcdf3-447">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fcdf3-448">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="fcdf3-448">Obtain data from an app</span></span>

<span data-ttu-id="fcdf3-449">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fcdf3-450">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fcdf3-451">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fcdf3-452">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fcdf3-453">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="fcdf3-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="fcdf3-454">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fcdf3-455">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fcdf3-456">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fcdf3-457">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-458">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fcdf3-459">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fcdf3-460">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fcdf3-461">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-462">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fcdf3-463">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fcdf3-464">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fcdf3-465">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-466">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fcdf3-467">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="fcdf3-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fcdf3-468">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fcdf3-469">分析转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-469">Analyze the dump</span></span>

<span data-ttu-id="fcdf3-470">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fcdf3-471">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fcdf3-472">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-472">Clear package caches</span></span>

<span data-ttu-id="fcdf3-473">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fcdf3-474">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fcdf3-475">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fcdf3-476">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fcdf3-477">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fcdf3-478">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fcdf3-479">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fcdf3-480">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fcdf3-481">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcdf3-482">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fcdf3-483">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-483">Azure documentation</span></span>

* [<span data-ttu-id="fcdf3-484">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fcdf3-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fcdf3-485">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="fcdf3-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fcdf3-486">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="fcdf3-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fcdf3-487">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fcdf3-488">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="fcdf3-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fcdf3-489">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fcdf3-490">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-491">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="fcdf3-492">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="fcdf3-493">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fcdf3-494">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="fcdf3-495">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fcdf3-496">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fcdf3-497">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fcdf3-498">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fcdf3-499">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fcdf3-500">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fcdf3-501">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fcdf3-502">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fcdf3-503">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fcdf3-504">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fcdf3-505">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fcdf3-506">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fcdf3-507">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fcdf3-508">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fcdf3-509">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fcdf3-510">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fcdf3-511">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fcdf3-512">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-512">App startup errors</span></span>

<span data-ttu-id="fcdf3-513">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fcdf3-514">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fcdf3-515">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fcdf3-515">403.14 Forbidden</span></span>

<span data-ttu-id="fcdf3-516">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-516">The app fails to start.</span></span> <span data-ttu-id="fcdf3-517">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fcdf3-518">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fcdf3-519">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-520">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-521">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fcdf3-522">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-522">Perform the following steps:</span></span>

1. <span data-ttu-id="fcdf3-523">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-524">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fcdf3-525">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fcdf3-526">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fcdf3-527">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fcdf3-528">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fcdf3-529">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fcdf3-530">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fcdf3-531">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-531">500 Internal Server Error</span></span>

<span data-ttu-id="fcdf3-532">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fcdf3-533">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fcdf3-534">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fcdf3-535">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fcdf3-536">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fcdf3-537">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fcdf3-538">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fcdf3-539">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fcdf3-540">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-540">The worker process fails.</span></span> <span data-ttu-id="fcdf3-541">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-541">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-542">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="fcdf3-543">检查：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-543">Check that:</span></span>

* <span data-ttu-id="fcdf3-544">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="fcdf3-545">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="fcdf3-546">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fcdf3-547">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-547">The worker process fails.</span></span> <span data-ttu-id="fcdf3-548">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-548">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-549">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到进程外托管请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="fcdf3-550">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fcdf3-551">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-551">502.5 Process Failure</span></span>

<span data-ttu-id="fcdf3-552">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-552">The worker process fails.</span></span> <span data-ttu-id="fcdf3-553">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-553">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-554">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fcdf3-555">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fcdf3-556">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fcdf3-557">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fcdf3-558">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fcdf3-559">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fcdf3-560">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fcdf3-561">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fcdf3-562">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fcdf3-563">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fcdf3-564">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fcdf3-565">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fcdf3-566">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fcdf3-567">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fcdf3-568">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fcdf3-569">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fcdf3-570">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fcdf3-571">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fcdf3-572">连接重置</span><span class="sxs-lookup"><span data-stu-id="fcdf3-572">Connection reset</span></span>

<span data-ttu-id="fcdf3-573">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fcdf3-574">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fcdf3-575">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fcdf3-576">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fcdf3-577">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="fcdf3-577">Default startup limits</span></span>

<span data-ttu-id="fcdf3-578">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fcdf3-579">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fcdf3-580">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fcdf3-581">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fcdf3-582">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-583">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fcdf3-584">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fcdf3-585">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fcdf3-586">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fcdf3-587">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fcdf3-588">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fcdf3-589">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fcdf3-590">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-591">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-592">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-593">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-594">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fcdf3-595">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fcdf3-596">检查日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-596">Examine the log.</span></span> <span data-ttu-id="fcdf3-597">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fcdf3-598">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="fcdf3-599">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-600">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fcdf3-601">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-602">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-603">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-604">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fcdf3-605">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fcdf3-606">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fcdf3-607">运行应用：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-607">Run the app:</span></span>
   * <span data-ttu-id="fcdf3-608">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fcdf3-609">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fcdf3-610">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-611">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-612">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-614">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-615">应用的控制台输出（显示任何错误）会传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fcdf3-616">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fcdf3-617">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-617">**Current release**</span></span>

* <span data-ttu-id="fcdf3-618">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fcdf3-619">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fcdf3-620">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fcdf3-621">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fcdf3-622">应用的控制台输出（显示任何错误）会传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-623">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-624">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-626">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-627">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fcdf3-628">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-629">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-630">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-631">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fcdf3-632">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="fcdf3-633">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮  。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="fcdf3-634">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-635">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fcdf3-636">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-637">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fcdf3-638">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-639">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-639">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-640">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-640">Return to the Azure portal.</span></span> <span data-ttu-id="fcdf3-641">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fcdf3-642">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-643">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-644">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-645">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fcdf3-646">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="fcdf3-647">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="fcdf3-648">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fcdf3-649">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="fcdf3-650">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="fcdf3-651">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fcdf3-652">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="fcdf3-653">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-654">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-655">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fcdf3-656">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fcdf3-657">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-658">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="fcdf3-659">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-660">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="fcdf3-661">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-662">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="fcdf3-663">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="fcdf3-664">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-664">Redeploy the app.</span></span>
   * <span data-ttu-id="fcdf3-665">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="fcdf3-666">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-667">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-668">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="fcdf3-669">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="fcdf3-670">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-671">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="fcdf3-672">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="fcdf3-673">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="fcdf3-674">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-675">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-676">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-677">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-678">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-679">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="fcdf3-680">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="fcdf3-681">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="fcdf3-682">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="fcdf3-683">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="fcdf3-684">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="fcdf3-685">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="fcdf3-686">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-686">Save the file.</span></span>

<span data-ttu-id="fcdf3-687">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-688">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-689">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-689">There's no limit on log file size.</span></span> <span data-ttu-id="fcdf3-690">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fcdf3-691">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-692">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fcdf3-693">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-694">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fcdf3-695">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-696">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="fcdf3-697">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="fcdf3-697">Monitoring blades</span></span>

<span data-ttu-id="fcdf3-698">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fcdf3-699">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fcdf3-700">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fcdf3-701">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fcdf3-702">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fcdf3-703">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fcdf3-704">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fcdf3-705">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fcdf3-706">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fcdf3-707">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fcdf3-708">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fcdf3-709">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fcdf3-710">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fcdf3-711">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-712">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-713">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-714">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fcdf3-715">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-716">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fcdf3-717">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fcdf3-718">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fcdf3-719">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fcdf3-720">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fcdf3-721">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fcdf3-722">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fcdf3-723">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fcdf3-724">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-724">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-725">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fcdf3-726">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fcdf3-727">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fcdf3-728">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fcdf3-729">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fcdf3-730">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fcdf3-731">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-732">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-733">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-734">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-735">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fcdf3-736">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fcdf3-737">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="fcdf3-738">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fcdf3-739">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fcdf3-740">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fcdf3-741">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fcdf3-742">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fcdf3-743">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fcdf3-744">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-744">Run the app at a command prompt</span></span>

<span data-ttu-id="fcdf3-745">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-746">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fcdf3-747">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-747">Framework-dependent deployment</span></span>

<span data-ttu-id="fcdf3-748">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fcdf3-749">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fcdf3-750">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fcdf3-751">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-752">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-753">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-754">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fcdf3-755">独立部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-755">Self-contained deployment</span></span>

<span data-ttu-id="fcdf3-756">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fcdf3-757">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fcdf3-758">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fcdf3-759">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-760">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-761">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-762">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fcdf3-763">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fcdf3-764">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-765">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-766">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fcdf3-767">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fcdf3-768">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-768">Edit the *web.config* file.</span></span> <span data-ttu-id="fcdf3-769">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fcdf3-770">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fcdf3-771">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fcdf3-772">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fcdf3-773">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fcdf3-774">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-775">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-775">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-776">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fcdf3-777">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fcdf3-778">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-778">Study the log for errors.</span></span>

<span data-ttu-id="fcdf3-779">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fcdf3-780">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-781">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fcdf3-782">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-782">Save the file.</span></span>

<span data-ttu-id="fcdf3-783">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-784">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-785">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-786">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-787">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="fcdf3-788">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="fcdf3-789">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fcdf3-790">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="fcdf3-791">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fcdf3-792">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="fcdf3-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fcdf3-793">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fcdf3-794">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="fcdf3-795">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fcdf3-796">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fcdf3-797">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fcdf3-798">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="fcdf3-798">Obtain data from an app</span></span>

<span data-ttu-id="fcdf3-799">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fcdf3-800">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fcdf3-801">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fcdf3-802">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fcdf3-803">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="fcdf3-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="fcdf3-804">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fcdf3-805">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fcdf3-806">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fcdf3-807">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-808">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fcdf3-809">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fcdf3-810">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fcdf3-811">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-812">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fcdf3-813">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fcdf3-814">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fcdf3-815">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-816">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fcdf3-817">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="fcdf3-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fcdf3-818">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fcdf3-819">分析转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-819">Analyze the dump</span></span>

<span data-ttu-id="fcdf3-820">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fcdf3-821">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fcdf3-822">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-822">Clear package caches</span></span>

<span data-ttu-id="fcdf3-823">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fcdf3-824">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fcdf3-825">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fcdf3-826">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fcdf3-827">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fcdf3-828">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fcdf3-829">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fcdf3-830">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fcdf3-831">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcdf3-832">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fcdf3-833">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-833">Azure documentation</span></span>

* [<span data-ttu-id="fcdf3-834">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fcdf3-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fcdf3-835">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="fcdf3-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fcdf3-836">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="fcdf3-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fcdf3-837">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fcdf3-838">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="fcdf3-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fcdf3-839">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fcdf3-840">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-841">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="fcdf3-842">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="fcdf3-843">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fcdf3-844">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="fcdf3-845">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fcdf3-846">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fcdf3-847">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fcdf3-848">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fcdf3-849">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fcdf3-850">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fcdf3-851">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fcdf3-852">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fcdf3-853">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fcdf3-854">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fcdf3-855">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fcdf3-856">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fcdf3-857">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fcdf3-858">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fcdf3-859">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fcdf3-860">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fcdf3-861">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fcdf3-862">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-862">App startup errors</span></span>

<span data-ttu-id="fcdf3-863">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fcdf3-864">本地调试时出现的“502.5 - 进程失败”可以使用本主题中的建议进行诊断。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fcdf3-865">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fcdf3-865">403.14 Forbidden</span></span>

<span data-ttu-id="fcdf3-866">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-866">The app fails to start.</span></span> <span data-ttu-id="fcdf3-867">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fcdf3-868">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fcdf3-869">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-870">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fcdf3-871">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fcdf3-872">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-872">Perform the following steps:</span></span>

1. <span data-ttu-id="fcdf3-873">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-874">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fcdf3-875">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fcdf3-876">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fcdf3-877">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fcdf3-878">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fcdf3-879">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fcdf3-880">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fcdf3-881">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-881">500 Internal Server Error</span></span>

<span data-ttu-id="fcdf3-882">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fcdf3-883">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fcdf3-884">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fcdf3-885">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fcdf3-886">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fcdf3-887">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fcdf3-888">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fcdf3-889">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="fcdf3-889">502.5 Process Failure</span></span>

<span data-ttu-id="fcdf3-890">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-890">The worker process fails.</span></span> <span data-ttu-id="fcdf3-891">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-891">The app doesn't start.</span></span>

<span data-ttu-id="fcdf3-892">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fcdf3-893">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fcdf3-894">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fcdf3-895">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fcdf3-896">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fcdf3-897">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fcdf3-898">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fcdf3-899">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fcdf3-900">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fcdf3-901">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fcdf3-902">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fcdf3-903">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fcdf3-904">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fcdf3-905">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fcdf3-906">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fcdf3-907">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fcdf3-908">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fcdf3-909">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fcdf3-910">连接重置</span><span class="sxs-lookup"><span data-stu-id="fcdf3-910">Connection reset</span></span>

<span data-ttu-id="fcdf3-911">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fcdf3-912">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fcdf3-913">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fcdf3-914">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fcdf3-915">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="fcdf3-915">Default startup limits</span></span>

<span data-ttu-id="fcdf3-916">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fcdf3-917">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fcdf3-918">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fcdf3-919">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fcdf3-920">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-921">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fcdf3-922">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fcdf3-923">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fcdf3-924">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fcdf3-925">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fcdf3-926">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fcdf3-927">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fcdf3-928">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-929">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-930">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-931">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-932">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fcdf3-933">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fcdf3-934">检查日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-934">Examine the log.</span></span> <span data-ttu-id="fcdf3-935">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fcdf3-936">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="fcdf3-937">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-938">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fcdf3-939">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fcdf3-940">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-941">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-942">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fcdf3-943">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fcdf3-944">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fcdf3-945">运行应用：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-945">Run the app:</span></span>
   * <span data-ttu-id="fcdf3-946">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fcdf3-947">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fcdf3-948">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-949">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-950">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-952">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-953">应用的控制台输出（显示任何错误）会传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fcdf3-954">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fcdf3-955">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-955">**Current release**</span></span>

* <span data-ttu-id="fcdf3-956">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fcdf3-957">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fcdf3-958">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fcdf3-959">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fcdf3-960">应用的控制台输出（显示任何错误）会传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fcdf3-961">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="fcdf3-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fcdf3-962">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fcdf3-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fcdf3-964">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fcdf3-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fcdf3-965">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fcdf3-966">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-967">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-968">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-969">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fcdf3-970">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="fcdf3-971">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮  。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="fcdf3-972">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fcdf3-973">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fcdf3-974">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-975">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fcdf3-976">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-977">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-977">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-978">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-978">Return to the Azure portal.</span></span> <span data-ttu-id="fcdf3-979">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fcdf3-980">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-981">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-982">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-983">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fcdf3-984">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="fcdf3-985">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="fcdf3-986">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fcdf3-987">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="fcdf3-988">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="fcdf3-989">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fcdf3-990">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="fcdf3-991">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-992">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-993">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fcdf3-994">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fcdf3-995">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-996">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fcdf3-997">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fcdf3-998">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fcdf3-999">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-1000">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="fcdf3-1001">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1001">Monitoring blades</span></span>

<span data-ttu-id="fcdf3-1002">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fcdf3-1003">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fcdf3-1004">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fcdf3-1005">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fcdf3-1006">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fcdf3-1007">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fcdf3-1008">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fcdf3-1009">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fcdf3-1010">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fcdf3-1011">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fcdf3-1012">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fcdf3-1013">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fcdf3-1014">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fcdf3-1015">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fcdf3-1016">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fcdf3-1017">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fcdf3-1018">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fcdf3-1019">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-1020">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fcdf3-1021">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fcdf3-1022">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fcdf3-1023">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fcdf3-1024">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fcdf3-1025">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fcdf3-1026">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fcdf3-1027">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fcdf3-1028">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-1029">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fcdf3-1030">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fcdf3-1031">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fcdf3-1032">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fcdf3-1033">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fcdf3-1034">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fcdf3-1035">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-1036">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-1037">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-1038">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-1039">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fcdf3-1040">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fcdf3-1041">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="fcdf3-1042">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fcdf3-1043">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fcdf3-1044">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fcdf3-1045">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fcdf3-1046">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fcdf3-1047">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fcdf3-1048">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="fcdf3-1049">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fcdf3-1050">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fcdf3-1051">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="fcdf3-1052">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fcdf3-1053">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fcdf3-1054">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fcdf3-1055">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-1056">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-1057">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-1058">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fcdf3-1059">独立部署</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1059">Self-contained deployment</span></span>

<span data-ttu-id="fcdf3-1060">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fcdf3-1061">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fcdf3-1062">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fcdf3-1063">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fcdf3-1064">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fcdf3-1065">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fcdf3-1066">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fcdf3-1067">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fcdf3-1068">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fcdf3-1069">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fcdf3-1070">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fcdf3-1071">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fcdf3-1072">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="fcdf3-1073">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fcdf3-1074">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fcdf3-1075">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fcdf3-1076">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fcdf3-1077">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fcdf3-1078">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-1079">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="fcdf3-1080">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fcdf3-1081">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fcdf3-1082">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1082">Study the log for errors.</span></span>

<span data-ttu-id="fcdf3-1083">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fcdf3-1084">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fcdf3-1085">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fcdf3-1086">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1086">Save the file.</span></span>

<span data-ttu-id="fcdf3-1087">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-1088">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fcdf3-1089">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fcdf3-1090">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fcdf3-1091">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fcdf3-1092">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fcdf3-1093">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fcdf3-1094">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="fcdf3-1095">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fcdf3-1096">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fcdf3-1097">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fcdf3-1098">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1098">Obtain data from an app</span></span>

<span data-ttu-id="fcdf3-1099">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fcdf3-1100">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fcdf3-1101">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fcdf3-1102">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fcdf3-1103">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="fcdf3-1104">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fcdf3-1105">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fcdf3-1106">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fcdf3-1107">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-1108">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fcdf3-1109">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fcdf3-1110">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fcdf3-1111">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fcdf3-1112">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fcdf3-1113">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fcdf3-1114">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fcdf3-1115">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fcdf3-1116">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fcdf3-1117">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fcdf3-1118">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fcdf3-1119">分析转储</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1119">Analyze the dump</span></span>

<span data-ttu-id="fcdf3-1120">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fcdf3-1121">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fcdf3-1122">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1122">Clear package caches</span></span>

<span data-ttu-id="fcdf3-1123">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fcdf3-1124">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fcdf3-1125">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fcdf3-1126">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fcdf3-1127">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fcdf3-1128">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fcdf3-1129">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fcdf3-1130">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fcdf3-1131">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcdf3-1132">其他资源</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fcdf3-1133">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1133">Azure documentation</span></span>

* [<span data-ttu-id="fcdf3-1134">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fcdf3-1135">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fcdf3-1136">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fcdf3-1137">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fcdf3-1138">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fcdf3-1139">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fcdf3-1140">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fcdf3-1141">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="fcdf3-1142">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="fcdf3-1143">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fcdf3-1144">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="fcdf3-1145">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fcdf3-1146">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fcdf3-1147">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fcdf3-1148">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fcdf3-1149">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="fcdf3-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
