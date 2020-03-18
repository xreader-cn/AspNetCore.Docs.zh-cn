---
title: 对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除
author: rick-anderson
description: 了解如何诊断Azure 应用服务和 Internet Information Services (IIS) 的 ASP.NET Core 应用部署问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644880"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="8feb7-103">对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="8feb7-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="8feb7-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="8feb7-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8feb7-105">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="8feb7-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="8feb7-106">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="8feb7-107">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="8feb7-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="8feb7-108">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="8feb7-109">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="8feb7-110">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="8feb7-111">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="8feb7-112">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="8feb7-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="8feb7-113">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="8feb7-114">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="8feb7-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="8feb7-115">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="8feb7-116">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="8feb7-117">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-117">App startup errors</span></span>

<span data-ttu-id="8feb7-118">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="8feb7-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="8feb7-119">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="8feb7-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="8feb7-120">403.14 Forbidden</span></span>

<span data-ttu-id="8feb7-121">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-121">The app fails to start.</span></span> <span data-ttu-id="8feb7-122">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="8feb7-123">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="8feb7-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="8feb7-124">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-125">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-126">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="8feb7-127">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-127">Perform the following steps:</span></span>

1. <span data-ttu-id="8feb7-128">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-129">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="8feb7-130">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="8feb7-131">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="8feb7-132">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="8feb7-133">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="8feb7-134">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="8feb7-135">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="8feb7-136">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-136">500 Internal Server Error</span></span>

<span data-ttu-id="8feb7-137">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="8feb7-138">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="8feb7-139">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="8feb7-140">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="8feb7-141">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="8feb7-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="8feb7-142">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="8feb7-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="8feb7-143">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="8feb7-144">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="8feb7-145">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-145">The worker process fails.</span></span> <span data-ttu-id="8feb7-146">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-146">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-147">加载 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="8feb7-148">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-148">Take one of the following actions:</span></span>

* <span data-ttu-id="8feb7-149">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”）   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="8feb7-150">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="8feb7-151">在 [GitHub 存储库](https://github.com/dotnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="8feb7-152">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="8feb7-153">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-153">The worker process fails.</span></span> <span data-ttu-id="8feb7-154">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-154">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-155">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="8feb7-156">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="8feb7-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="8feb7-157">常见失败情况：</span><span class="sxs-lookup"><span data-stu-id="8feb7-157">Common failure conditions:</span></span>

* <span data-ttu-id="8feb7-158">由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="8feb7-159">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="8feb7-160">使用 Azure Key Vault 时，缺少对 Key Vault 的权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="8feb7-161">检查目标 Key Vault 中的访问策略，确保授予了正确的权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="8feb7-162">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="8feb7-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="8feb7-163">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-163">The worker process fails.</span></span> <span data-ttu-id="8feb7-164">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-164">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-165">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="8feb7-166">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="8feb7-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="8feb7-167">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="8feb7-168">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="8feb7-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="8feb7-169">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="8feb7-170">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-170">To fix this error, either:</span></span>

* <span data-ttu-id="8feb7-171">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="8feb7-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="8feb7-172">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="8feb7-173">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="8feb7-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="8feb7-174">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="8feb7-175">进程启动失败的原因也可在“应用程序事件日志”中找到。</span><span class="sxs-lookup"><span data-stu-id="8feb7-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="8feb7-176">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="8feb7-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="8feb7-177">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-177">The worker process fails.</span></span> <span data-ttu-id="8feb7-178">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-178">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-179">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="8feb7-180">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="8feb7-181">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-181">To fix this error, either:</span></span>

* <span data-ttu-id="8feb7-182">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="8feb7-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="8feb7-183">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="8feb7-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="8feb7-184">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="8feb7-185">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-185">The worker process fails.</span></span> <span data-ttu-id="8feb7-186">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-186">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-187">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="8feb7-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="8feb7-188">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="8feb7-189">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="8feb7-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="8feb7-190">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="8feb7-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="8feb7-191">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="8feb7-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="8feb7-192">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="8feb7-193">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="8feb7-194">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="8feb7-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="8feb7-195">工作进程不能在同一进程中运行多个进程内应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="8feb7-196">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="8feb7-197">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="8feb7-198">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="8feb7-199">这表示 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="8feb7-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="8feb7-200">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="8feb7-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="8feb7-201">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="8feb7-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="8feb7-202">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="8feb7-203">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="8feb7-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="8feb7-204">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="8feb7-205">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="8feb7-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="8feb7-206">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="8feb7-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="8feb7-207">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-207">502.5 Process Failure</span></span>

<span data-ttu-id="8feb7-208">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-208">The worker process fails.</span></span> <span data-ttu-id="8feb7-209">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-209">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-210">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="8feb7-211">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="8feb7-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="8feb7-212">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="8feb7-213">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="8feb7-214"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="8feb7-215">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="8feb7-216">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="8feb7-217">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="8feb7-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="8feb7-218">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="8feb7-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="8feb7-219">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="8feb7-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="8feb7-220">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="8feb7-221">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="8feb7-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="8feb7-222">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="8feb7-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="8feb7-223">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="8feb7-224">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="8feb7-225">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="8feb7-226">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="8feb7-227">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="8feb7-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="8feb7-228">连接重置</span><span class="sxs-lookup"><span data-stu-id="8feb7-228">Connection reset</span></span>

<span data-ttu-id="8feb7-229">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="8feb7-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="8feb7-230">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="8feb7-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="8feb7-231">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="8feb7-232">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="8feb7-233">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="8feb7-233">Default startup limits</span></span>

<span data-ttu-id="8feb7-234">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="8feb7-235">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="8feb7-236">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="8feb7-237">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="8feb7-238">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-239">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="8feb7-240">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="8feb7-241">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="8feb7-242">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="8feb7-243">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="8feb7-244">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="8feb7-245">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="8feb7-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="8feb7-246">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-247">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-248">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-249">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-250">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-251">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="8feb7-252">检查日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-252">Examine the log.</span></span> <span data-ttu-id="8feb7-253">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="8feb7-254">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="8feb7-255">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-256">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="8feb7-257">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-258">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-259">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-260">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="8feb7-261">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="8feb7-262">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="8feb7-263">运行应用：</span><span class="sxs-lookup"><span data-stu-id="8feb7-263">Run the app:</span></span>
   * <span data-ttu-id="8feb7-264">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="8feb7-265">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="8feb7-266">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-267">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-268">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-270">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-271">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="8feb7-272">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="8feb7-273">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-273">**Current release**</span></span>

* <span data-ttu-id="8feb7-274">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="8feb7-275">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="8feb7-276">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="8feb7-277">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="8feb7-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="8feb7-278">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-279">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-280">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-282">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-283">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="8feb7-284">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-285">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="8feb7-286">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-287">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-288">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="8feb7-289">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="8feb7-290">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-291">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-292">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-293">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-294">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-295">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-295">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-296">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="8feb7-296">Return to the Azure portal.</span></span> <span data-ttu-id="8feb7-297">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-298">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-299">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-300">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-301">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-302">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="8feb7-303">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="8feb7-304">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-305">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="8feb7-306">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="8feb7-307">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-308">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="8feb7-309">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-310">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-311">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="8feb7-312">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="8feb7-313">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-314">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="8feb7-315">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-316">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="8feb7-317">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-318">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="8feb7-319">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="8feb7-320">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-320">Redeploy the app.</span></span>
   * <span data-ttu-id="8feb7-321">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="8feb7-322">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-323">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-324">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="8feb7-325">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="8feb7-326">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-327">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="8feb7-328">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="8feb7-329">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="8feb7-330">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-331">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-332">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-333">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-334">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-335">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="8feb7-336">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="8feb7-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="8feb7-337">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="8feb7-338">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="8feb7-339">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="8feb7-340">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="8feb7-341">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="8feb7-342">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-342">Save the file.</span></span>

<span data-ttu-id="8feb7-343">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-344">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-345">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-345">There's no limit on log file size.</span></span> <span data-ttu-id="8feb7-346">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="8feb7-347">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-348">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="8feb7-349">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="8feb7-350">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="8feb7-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="8feb7-351">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-352">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="8feb7-353">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="8feb7-353">Monitoring blades</span></span>

<span data-ttu-id="8feb7-354">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="8feb7-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="8feb7-355">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="8feb7-356">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="8feb7-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="8feb7-357">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="8feb7-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="8feb7-358">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="8feb7-359">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="8feb7-360">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="8feb7-361">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="8feb7-362">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="8feb7-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="8feb7-363">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="8feb7-364">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="8feb7-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="8feb7-365">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="8feb7-366">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-367">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-368">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-369">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-370">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-371">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-372">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-373">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="8feb7-374">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="8feb7-375">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="8feb7-376">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="8feb7-377">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="8feb7-378">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="8feb7-379">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="8feb7-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="8feb7-380">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-380">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-381">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="8feb7-382">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="8feb7-383">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="8feb7-384">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-385">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="8feb7-386">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="8feb7-387">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-388">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-389">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-390">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-391">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="8feb7-392">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="8feb7-393">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="8feb7-394">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="8feb7-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="8feb7-395">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8feb7-396">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="8feb7-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8feb7-397">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8feb7-398">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="8feb7-399">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="8feb7-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8feb7-400">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-400">Run the app at a command prompt</span></span>

<span data-ttu-id="8feb7-401">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-402">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="8feb7-403">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-403">Framework-dependent deployment</span></span>

<span data-ttu-id="8feb7-404">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="8feb7-405">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="8feb7-406">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="8feb7-407">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-408">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-409">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-410">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="8feb7-411">独立部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-411">Self-contained deployment</span></span>

<span data-ttu-id="8feb7-412">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="8feb7-413">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="8feb7-414">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="8feb7-415">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-416">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-417">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-418">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="8feb7-419">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="8feb7-420">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-421">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-422">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="8feb7-423">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="8feb7-424">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-424">Edit the *web.config* file.</span></span> <span data-ttu-id="8feb7-425">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="8feb7-426">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="8feb7-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="8feb7-427">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8feb7-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="8feb7-428">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="8feb7-429">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="8feb7-430">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-431">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-431">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-432">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="8feb7-433">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="8feb7-434">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-434">Study the log for errors.</span></span>

<span data-ttu-id="8feb7-435">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-436">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-437">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-438">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-438">Save the file.</span></span>

<span data-ttu-id="8feb7-439">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-440">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-441">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-442">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-443">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="8feb7-444">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="8feb7-445">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="8feb7-446">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="8feb7-447">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="8feb7-448">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="8feb7-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="8feb7-449">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="8feb7-450">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="8feb7-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="8feb7-451">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="8feb7-452">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="8feb7-453">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="8feb7-454">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="8feb7-454">Obtain data from an app</span></span>

<span data-ttu-id="8feb7-455">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="8feb7-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="8feb7-456">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="8feb7-457">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="8feb7-458">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8feb7-459">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="8feb7-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="8feb7-460">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="8feb7-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8feb7-461">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="8feb7-462">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="8feb7-463">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-463">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-464">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="8feb7-465">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="8feb7-466">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8feb7-467">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-468">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="8feb7-469">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="8feb7-470">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8feb7-471">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-472">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8feb7-473">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="8feb7-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8feb7-474">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="8feb7-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8feb7-475">分析转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-475">Analyze the dump</span></span>

<span data-ttu-id="8feb7-476">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="8feb7-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8feb7-477">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="8feb7-478">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-478">Clear package caches</span></span>

<span data-ttu-id="8feb7-479">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8feb7-480">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8feb7-481">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="8feb7-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8feb7-482">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8feb7-483">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="8feb7-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8feb7-484">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="8feb7-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8feb7-485">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="8feb7-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8feb7-486">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="8feb7-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8feb7-487">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8feb7-488">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="8feb7-489">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-489">Azure documentation</span></span>

* [<span data-ttu-id="8feb7-490">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="8feb7-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="8feb7-491">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="8feb7-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="8feb7-492">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="8feb7-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="8feb7-493">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="8feb7-494">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="8feb7-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="8feb7-495">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="8feb7-496">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-497">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="8feb7-498">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="8feb7-498">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="8feb7-499">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="8feb7-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="8feb7-500">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="8feb7-501">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="8feb7-502">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="8feb7-503">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="8feb7-504">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-504">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="8feb7-505">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-505">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8feb7-506">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="8feb7-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="8feb7-507">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="8feb7-508">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="8feb7-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="8feb7-509">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="8feb7-510">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="8feb7-511">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="8feb7-512">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="8feb7-513">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="8feb7-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="8feb7-514">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="8feb7-515">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="8feb7-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="8feb7-516">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="8feb7-517">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="8feb7-518">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-518">App startup errors</span></span>

<span data-ttu-id="8feb7-519">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="8feb7-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="8feb7-520">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="8feb7-521">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="8feb7-521">403.14 Forbidden</span></span>

<span data-ttu-id="8feb7-522">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-522">The app fails to start.</span></span> <span data-ttu-id="8feb7-523">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="8feb7-524">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="8feb7-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="8feb7-525">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-526">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-527">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="8feb7-528">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-528">Perform the following steps:</span></span>

1. <span data-ttu-id="8feb7-529">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-530">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="8feb7-531">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="8feb7-532">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="8feb7-533">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="8feb7-534">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="8feb7-535">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="8feb7-536">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="8feb7-537">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-537">500 Internal Server Error</span></span>

<span data-ttu-id="8feb7-538">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="8feb7-539">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="8feb7-540">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="8feb7-541">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="8feb7-542">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="8feb7-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="8feb7-543">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="8feb7-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="8feb7-544">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="8feb7-545">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="8feb7-546">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-546">The worker process fails.</span></span> <span data-ttu-id="8feb7-547">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-547">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-548">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="8feb7-549">检查：</span><span class="sxs-lookup"><span data-stu-id="8feb7-549">Check that:</span></span>

* <span data-ttu-id="8feb7-550">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="8feb7-551">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="8feb7-552">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="8feb7-553">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-553">The worker process fails.</span></span> <span data-ttu-id="8feb7-554">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-554">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-555">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到进程外托管请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="8feb7-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="8feb7-556">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="8feb7-557">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-557">502.5 Process Failure</span></span>

<span data-ttu-id="8feb7-558">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-558">The worker process fails.</span></span> <span data-ttu-id="8feb7-559">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-559">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-560">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="8feb7-561">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="8feb7-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="8feb7-562">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="8feb7-563">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="8feb7-564"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="8feb7-565">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="8feb7-566">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="8feb7-567">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="8feb7-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="8feb7-568">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="8feb7-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="8feb7-569">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="8feb7-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="8feb7-570">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="8feb7-571">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="8feb7-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="8feb7-572">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="8feb7-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="8feb7-573">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="8feb7-574">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="8feb7-575">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="8feb7-576">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="8feb7-577">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="8feb7-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="8feb7-578">连接重置</span><span class="sxs-lookup"><span data-stu-id="8feb7-578">Connection reset</span></span>

<span data-ttu-id="8feb7-579">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="8feb7-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="8feb7-580">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="8feb7-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="8feb7-581">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="8feb7-582">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="8feb7-583">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="8feb7-583">Default startup limits</span></span>

<span data-ttu-id="8feb7-584">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="8feb7-585">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="8feb7-586">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="8feb7-587">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="8feb7-588">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-589">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="8feb7-590">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="8feb7-591">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="8feb7-592">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="8feb7-593">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="8feb7-594">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="8feb7-595">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="8feb7-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="8feb7-596">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-597">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-598">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-599">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-600">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-601">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="8feb7-602">检查日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-602">Examine the log.</span></span> <span data-ttu-id="8feb7-603">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="8feb7-604">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="8feb7-605">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-606">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="8feb7-607">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-608">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-609">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-610">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="8feb7-611">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="8feb7-612">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="8feb7-613">运行应用：</span><span class="sxs-lookup"><span data-stu-id="8feb7-613">Run the app:</span></span>
   * <span data-ttu-id="8feb7-614">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="8feb7-615">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="8feb7-616">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-617">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-618">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-620">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-621">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="8feb7-622">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="8feb7-623">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-623">**Current release**</span></span>

* <span data-ttu-id="8feb7-624">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="8feb7-625">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="8feb7-626">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="8feb7-627">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="8feb7-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="8feb7-628">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-629">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-630">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-632">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-633">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="8feb7-634">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-635">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="8feb7-636">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-637">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-638">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="8feb7-639">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="8feb7-640">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-641">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-642">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-643">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-644">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-645">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-645">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-646">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="8feb7-646">Return to the Azure portal.</span></span> <span data-ttu-id="8feb7-647">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-648">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-649">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-650">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-651">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-652">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="8feb7-653">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="8feb7-654">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-655">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="8feb7-656">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="8feb7-657">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-658">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="8feb7-659">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-660">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-661">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="8feb7-662">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="8feb7-663">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-664">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="8feb7-665">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-666">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="8feb7-667">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-668">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="8feb7-669">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="8feb7-670">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-670">Redeploy the app.</span></span>
   * <span data-ttu-id="8feb7-671">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="8feb7-672">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-673">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-674">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="8feb7-675">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="8feb7-676">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-677">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="8feb7-678">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="8feb7-679">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="8feb7-680">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-681">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-682">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-683">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-684">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-685">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="8feb7-686">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="8feb7-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="8feb7-687">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="8feb7-688">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="8feb7-689">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="8feb7-690">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="8feb7-691">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="8feb7-692">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-692">Save the file.</span></span>

<span data-ttu-id="8feb7-693">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-694">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-695">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-695">There's no limit on log file size.</span></span> <span data-ttu-id="8feb7-696">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="8feb7-697">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-698">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="8feb7-699">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="8feb7-700">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="8feb7-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="8feb7-701">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-702">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="8feb7-703">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="8feb7-703">Monitoring blades</span></span>

<span data-ttu-id="8feb7-704">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="8feb7-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="8feb7-705">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="8feb7-706">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="8feb7-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="8feb7-707">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="8feb7-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="8feb7-708">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="8feb7-709">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="8feb7-710">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="8feb7-711">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="8feb7-712">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="8feb7-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="8feb7-713">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="8feb7-714">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="8feb7-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="8feb7-715">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="8feb7-716">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-717">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-718">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-719">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-720">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-721">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-722">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-723">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="8feb7-724">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="8feb7-725">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="8feb7-726">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="8feb7-727">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="8feb7-728">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="8feb7-729">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="8feb7-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="8feb7-730">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-730">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-731">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="8feb7-732">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="8feb7-733">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="8feb7-734">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-735">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="8feb7-736">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="8feb7-737">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-738">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-739">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-740">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-741">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="8feb7-742">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="8feb7-743">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="8feb7-744">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="8feb7-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="8feb7-745">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8feb7-746">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="8feb7-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8feb7-747">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8feb7-748">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="8feb7-749">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="8feb7-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8feb7-750">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-750">Run the app at a command prompt</span></span>

<span data-ttu-id="8feb7-751">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-752">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="8feb7-753">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-753">Framework-dependent deployment</span></span>

<span data-ttu-id="8feb7-754">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="8feb7-755">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="8feb7-756">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="8feb7-757">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-758">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-759">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-760">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="8feb7-761">独立部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-761">Self-contained deployment</span></span>

<span data-ttu-id="8feb7-762">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="8feb7-763">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="8feb7-764">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="8feb7-765">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-766">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-767">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-768">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="8feb7-769">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="8feb7-770">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-771">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-772">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="8feb7-773">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="8feb7-774">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-774">Edit the *web.config* file.</span></span> <span data-ttu-id="8feb7-775">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="8feb7-776">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="8feb7-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="8feb7-777">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8feb7-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="8feb7-778">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="8feb7-779">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="8feb7-780">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-781">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-781">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-782">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="8feb7-783">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="8feb7-784">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-784">Study the log for errors.</span></span>

<span data-ttu-id="8feb7-785">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-786">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-787">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-788">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-788">Save the file.</span></span>

<span data-ttu-id="8feb7-789">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-790">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-791">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-792">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-793">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="8feb7-794">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="8feb7-795">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="8feb7-796">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="8feb7-797">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="8feb7-798">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="8feb7-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="8feb7-799">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="8feb7-800">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="8feb7-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="8feb7-801">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="8feb7-802">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="8feb7-803">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="8feb7-804">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="8feb7-804">Obtain data from an app</span></span>

<span data-ttu-id="8feb7-805">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="8feb7-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="8feb7-806">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="8feb7-807">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="8feb7-808">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8feb7-809">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="8feb7-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="8feb7-810">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="8feb7-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8feb7-811">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="8feb7-812">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="8feb7-813">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-813">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-814">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="8feb7-815">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="8feb7-816">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8feb7-817">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-818">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="8feb7-819">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="8feb7-820">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8feb7-821">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-822">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8feb7-823">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="8feb7-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8feb7-824">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="8feb7-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8feb7-825">分析转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-825">Analyze the dump</span></span>

<span data-ttu-id="8feb7-826">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="8feb7-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8feb7-827">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="8feb7-828">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-828">Clear package caches</span></span>

<span data-ttu-id="8feb7-829">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8feb7-830">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8feb7-831">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="8feb7-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8feb7-832">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8feb7-833">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="8feb7-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8feb7-834">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="8feb7-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8feb7-835">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="8feb7-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8feb7-836">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="8feb7-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8feb7-837">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8feb7-838">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="8feb7-839">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-839">Azure documentation</span></span>

* [<span data-ttu-id="8feb7-840">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="8feb7-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="8feb7-841">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="8feb7-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="8feb7-842">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="8feb7-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="8feb7-843">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="8feb7-844">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="8feb7-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="8feb7-845">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="8feb7-846">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-847">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="8feb7-848">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="8feb7-848">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="8feb7-849">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="8feb7-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="8feb7-850">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="8feb7-851">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="8feb7-852">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="8feb7-853">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="8feb7-854">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-854">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="8feb7-855">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-855">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8feb7-856">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="8feb7-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="8feb7-857">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="8feb7-858">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="8feb7-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="8feb7-859">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="8feb7-860">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="8feb7-861">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="8feb7-862">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="8feb7-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="8feb7-863">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="8feb7-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="8feb7-864">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="8feb7-865">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="8feb7-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="8feb7-866">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="8feb7-867">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="8feb7-868">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-868">App startup errors</span></span>

<span data-ttu-id="8feb7-869">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="8feb7-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="8feb7-870">本地调试时出现的“502.5 - 进程失败”可以使用本主题中的建议进行诊断  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="8feb7-871">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="8feb7-871">403.14 Forbidden</span></span>

<span data-ttu-id="8feb7-872">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-872">The app fails to start.</span></span> <span data-ttu-id="8feb7-873">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="8feb7-874">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="8feb7-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="8feb7-875">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-876">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="8feb7-877">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="8feb7-878">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-878">Perform the following steps:</span></span>

1. <span data-ttu-id="8feb7-879">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-880">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="8feb7-881">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="8feb7-882">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="8feb7-883">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="8feb7-884">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="8feb7-885">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="8feb7-886">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="8feb7-887">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-887">500 Internal Server Error</span></span>

<span data-ttu-id="8feb7-888">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="8feb7-889">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="8feb7-890">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="8feb7-891">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="8feb7-892">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="8feb7-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="8feb7-893">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="8feb7-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="8feb7-894">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="8feb7-895">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="8feb7-895">502.5 Process Failure</span></span>

<span data-ttu-id="8feb7-896">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-896">The worker process fails.</span></span> <span data-ttu-id="8feb7-897">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="8feb7-897">The app doesn't start.</span></span>

<span data-ttu-id="8feb7-898">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="8feb7-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="8feb7-899">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="8feb7-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="8feb7-900">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="8feb7-901">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="8feb7-902"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="8feb7-903">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="8feb7-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="8feb7-904">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="8feb7-905">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="8feb7-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="8feb7-906">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="8feb7-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="8feb7-907">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="8feb7-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="8feb7-908">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="8feb7-909">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="8feb7-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="8feb7-910">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="8feb7-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="8feb7-911">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="8feb7-912">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="8feb7-913">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="8feb7-914">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="8feb7-915">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="8feb7-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="8feb7-916">连接重置</span><span class="sxs-lookup"><span data-stu-id="8feb7-916">Connection reset</span></span>

<span data-ttu-id="8feb7-917">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="8feb7-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="8feb7-918">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="8feb7-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="8feb7-919">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="8feb7-920">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="8feb7-921">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="8feb7-921">Default startup limits</span></span>

<span data-ttu-id="8feb7-922">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="8feb7-923">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="8feb7-924">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="8feb7-925">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="8feb7-926">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-927">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="8feb7-928">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="8feb7-929">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="8feb7-930">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="8feb7-931">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="8feb7-932">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="8feb7-933">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="8feb7-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="8feb7-934">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-935">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-936">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-937">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-938">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-939">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="8feb7-940">检查日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-940">Examine the log.</span></span> <span data-ttu-id="8feb7-941">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="8feb7-942">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="8feb7-943">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-944">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="8feb7-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="8feb7-945">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="8feb7-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="8feb7-946">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-947">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-948">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="8feb7-949">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="8feb7-950">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="8feb7-951">运行应用：</span><span class="sxs-lookup"><span data-stu-id="8feb7-951">Run the app:</span></span>
   * <span data-ttu-id="8feb7-952">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="8feb7-953">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="8feb7-954">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-955">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-956">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-958">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-959">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="8feb7-960">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="8feb7-961">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="8feb7-961">**Current release**</span></span>

* <span data-ttu-id="8feb7-962">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="8feb7-963">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="8feb7-964">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="8feb7-965">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="8feb7-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="8feb7-966">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="8feb7-967">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="8feb7-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="8feb7-968">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="8feb7-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="8feb7-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="8feb7-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="8feb7-970">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="8feb7-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="8feb7-971">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="8feb7-972">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="8feb7-973">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="8feb7-974">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-975">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-976">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="8feb7-977">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="8feb7-978">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="8feb7-979">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-980">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-981">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-982">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-983">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-983">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-984">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="8feb7-984">Return to the Azure portal.</span></span> <span data-ttu-id="8feb7-985">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-986">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-987">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-988">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-989">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="8feb7-990">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="8feb7-991">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="8feb7-992">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-993">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="8feb7-994">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="8feb7-995">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-996">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="8feb7-997">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-998">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-999">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="8feb7-1000">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="8feb7-1001">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-1002">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="8feb7-1003">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="8feb7-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="8feb7-1004">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="8feb7-1005">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-1006">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="8feb7-1007">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="8feb7-1007">Monitoring blades</span></span>

<span data-ttu-id="8feb7-1008">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="8feb7-1009">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="8feb7-1010">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="8feb7-1011">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="8feb7-1012">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="8feb7-1013">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="8feb7-1014">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="8feb7-1015">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="8feb7-1016">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="8feb7-1017">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="8feb7-1018">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="8feb7-1019">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="8feb7-1020">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="8feb7-1021">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="8feb7-1022">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="8feb7-1023">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="8feb7-1024">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="8feb7-1025">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-1026">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="8feb7-1027">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="8feb7-1028">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="8feb7-1029">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="8feb7-1030">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="8feb7-1031">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="8feb7-1032">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="8feb7-1033">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="8feb7-1034">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-1035">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="8feb7-1036">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="8feb7-1037">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="8feb7-1038">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="8feb7-1039">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="8feb7-1040">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="8feb7-1041">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-1042">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-1043">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-1044">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-1045">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="8feb7-1046">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="8feb7-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="8feb7-1047">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="8feb7-1048">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="8feb7-1049">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8feb7-1050">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8feb7-1051">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8feb7-1052">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="8feb7-1053">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8feb7-1054">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="8feb7-1055">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="8feb7-1056">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="8feb7-1057">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="8feb7-1058">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="8feb7-1059">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="8feb7-1060">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="8feb7-1061">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-1062">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-1063">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-1064">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="8feb7-1065">独立部署</span><span class="sxs-lookup"><span data-stu-id="8feb7-1065">Self-contained deployment</span></span>

<span data-ttu-id="8feb7-1066">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="8feb7-1067">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="8feb7-1068">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="8feb7-1069">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="8feb7-1070">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="8feb7-1071">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="8feb7-1072">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="8feb7-1073">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="8feb7-1074">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="8feb7-1075">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="8feb7-1076">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="8feb7-1077">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="8feb7-1078">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="8feb7-1079">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="8feb7-1080">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="8feb7-1081">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="8feb7-1082">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="8feb7-1083">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="8feb7-1084">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-1085">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="8feb7-1086">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="8feb7-1087">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="8feb7-1088">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1088">Study the log for errors.</span></span>

<span data-ttu-id="8feb7-1089">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="8feb7-1090">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="8feb7-1091">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="8feb7-1092">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1092">Save the file.</span></span>

<span data-ttu-id="8feb7-1093">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-1094">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="8feb7-1095">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="8feb7-1096">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8feb7-1097">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="8feb7-1098">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="8feb7-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="8feb7-1099">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="8feb7-1100">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="8feb7-1101">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="8feb7-1102">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="8feb7-1103">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="8feb7-1104">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="8feb7-1104">Obtain data from an app</span></span>

<span data-ttu-id="8feb7-1105">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="8feb7-1106">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="8feb7-1107">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="8feb7-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="8feb7-1108">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8feb7-1109">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="8feb7-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="8feb7-1110">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8feb7-1111">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="8feb7-1112">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="8feb7-1113">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1113">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-1114">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="8feb7-1115">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="8feb7-1116">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8feb7-1117">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="8feb7-1118">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="8feb7-1119">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="8feb7-1120">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8feb7-1121">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8feb7-1122">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8feb7-1123">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="8feb7-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8feb7-1124">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8feb7-1125">分析转储</span><span class="sxs-lookup"><span data-stu-id="8feb7-1125">Analyze the dump</span></span>

<span data-ttu-id="8feb7-1126">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8feb7-1127">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="8feb7-1128">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="8feb7-1128">Clear package caches</span></span>

<span data-ttu-id="8feb7-1129">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8feb7-1130">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8feb7-1131">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="8feb7-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8feb7-1132">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8feb7-1133">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8feb7-1134">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8feb7-1135">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8feb7-1136">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8feb7-1137">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="8feb7-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8feb7-1138">其他资源</span><span class="sxs-lookup"><span data-stu-id="8feb7-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="8feb7-1139">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-1139">Azure documentation</span></span>

* [<span data-ttu-id="8feb7-1140">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="8feb7-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="8feb7-1141">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="8feb7-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="8feb7-1142">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="8feb7-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="8feb7-1143">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="8feb7-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="8feb7-1144">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="8feb7-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="8feb7-1145">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="8feb7-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="8feb7-1146">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="8feb7-1147">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="8feb7-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="8feb7-1148">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="8feb7-1148">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="8feb7-1149">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="8feb7-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="8feb7-1150">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="8feb7-1151">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="8feb7-1152">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8feb7-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="8feb7-1153">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="8feb7-1154">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="8feb7-1154">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="8feb7-1155">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="8feb7-1155">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
