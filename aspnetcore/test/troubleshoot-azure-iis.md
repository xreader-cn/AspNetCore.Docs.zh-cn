---
title: 对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除
author: rick-anderson
description: 了解如何诊断Azure 应用服务和 Internet Information Services (IIS) 的 ASP.NET Core 应用部署问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 09b004abd423abc9cc8e83d3bb3fea1dddf09e14
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776625"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="29a69-103">对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="29a69-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="29a69-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="29a69-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29a69-105">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="29a69-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="29a69-106">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="29a69-107">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="29a69-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="29a69-108">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="29a69-109">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="29a69-110">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="29a69-111">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="29a69-112">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="29a69-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="29a69-113">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="29a69-114">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="29a69-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="29a69-115">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="29a69-116">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="29a69-117">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-117">App startup errors</span></span>

<span data-ttu-id="29a69-118">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="29a69-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="29a69-119">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断   。</span><span class="sxs-lookup"><span data-stu-id="29a69-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="29a69-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="29a69-120">403.14 Forbidden</span></span>

<span data-ttu-id="29a69-121">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-121">The app fails to start.</span></span> <span data-ttu-id="29a69-122">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="29a69-123">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="29a69-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="29a69-124">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-125">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="29a69-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-126">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="29a69-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="29a69-127">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-127">Perform the following steps:</span></span>

1. <span data-ttu-id="29a69-128">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-129">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="29a69-130">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="29a69-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="29a69-131">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="29a69-132">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="29a69-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="29a69-133">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="29a69-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="29a69-134">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="29a69-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="29a69-135">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="29a69-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="29a69-136">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="29a69-136">500 Internal Server Error</span></span>

<span data-ttu-id="29a69-137">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="29a69-138">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="29a69-139">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="29a69-140">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="29a69-141">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="29a69-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="29a69-142">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="29a69-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="29a69-143">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="29a69-144">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="29a69-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="29a69-145">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-145">The worker process fails.</span></span> <span data-ttu-id="29a69-146">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-146">The app doesn't start.</span></span>

<span data-ttu-id="29a69-147">加载 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="29a69-148">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-148">Take one of the following actions:</span></span>

* <span data-ttu-id="29a69-149">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”）   。</span><span class="sxs-lookup"><span data-stu-id="29a69-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="29a69-150">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="29a69-151">在 [GitHub 存储库](https://github.com/dotnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="29a69-152">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="29a69-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="29a69-153">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-153">The worker process fails.</span></span> <span data-ttu-id="29a69-154">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-154">The app doesn't start.</span></span>

<span data-ttu-id="29a69-155">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="29a69-156">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="29a69-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="29a69-157">常见失败情况：</span><span class="sxs-lookup"><span data-stu-id="29a69-157">Common failure conditions:</span></span>

* <span data-ttu-id="29a69-158">由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29a69-159">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="29a69-160">使用 Azure Key Vault 时，缺少对 Key Vault 的权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="29a69-161">检查目标 Key Vault 中的访问策略，确保授予了正确的权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="29a69-162">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="29a69-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="29a69-163">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-163">The worker process fails.</span></span> <span data-ttu-id="29a69-164">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-164">The app doesn't start.</span></span>

<span data-ttu-id="29a69-165">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="29a69-166">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="29a69-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="29a69-167">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="29a69-168">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="29a69-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="29a69-169">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="29a69-170">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-170">To fix this error, either:</span></span>

* <span data-ttu-id="29a69-171">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="29a69-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="29a69-172">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="29a69-173">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="29a69-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="29a69-174">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="29a69-175">进程启动失败的原因也可在“应用程序事件日志”中找到。</span><span class="sxs-lookup"><span data-stu-id="29a69-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="29a69-176">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="29a69-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="29a69-177">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-177">The worker process fails.</span></span> <span data-ttu-id="29a69-178">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-178">The app doesn't start.</span></span>

<span data-ttu-id="29a69-179">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="29a69-180">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="29a69-181">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-181">To fix this error, either:</span></span>

* <span data-ttu-id="29a69-182">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="29a69-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="29a69-183">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="29a69-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="29a69-184">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="29a69-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="29a69-185">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-185">The worker process fails.</span></span> <span data-ttu-id="29a69-186">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-186">The app doesn't start.</span></span>

<span data-ttu-id="29a69-187">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="29a69-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="29a69-188">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="29a69-189">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="29a69-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="29a69-190">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="29a69-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="29a69-191">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="29a69-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="29a69-192">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="29a69-193">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="29a69-194">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="29a69-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="29a69-195">工作进程不能在同一进程中运行多个进程内应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="29a69-196">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="29a69-197">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="29a69-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="29a69-198">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻   。</span><span class="sxs-lookup"><span data-stu-id="29a69-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="29a69-199">这表示 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="29a69-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="29a69-200">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="29a69-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="29a69-201">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="29a69-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="29a69-202">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="29a69-203">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="29a69-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="29a69-204">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="29a69-205">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="29a69-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="29a69-206">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="29a69-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="29a69-207">500.38 ANCM 找不到应用程序 DLL</span><span class="sxs-lookup"><span data-stu-id="29a69-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="29a69-208">ANCM 找不到应用程序 ANCM，该内容应显示在可执行文件的旁边。</span><span class="sxs-lookup"><span data-stu-id="29a69-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="29a69-209">在使用进程内托管模型托管打包为[单文件可执行程序](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)的应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="29a69-210">该进程内模型要求 ANCM 将 .NET Core 应用加载到现有 IIS 进程中。</span><span class="sxs-lookup"><span data-stu-id="29a69-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="29a69-211">单文件部署模型不支持此方案。</span><span class="sxs-lookup"><span data-stu-id="29a69-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="29a69-212">请在应用的项目文件中使用下述方法之一来修复此错误  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="29a69-213">通过将 `PublishSingleFile` MSBuild 属性设置为 `false` 来禁用单文件发布。</span><span class="sxs-lookup"><span data-stu-id="29a69-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="29a69-214">通过将 `AspNetCoreHostingModel` MSBuild 属性设置为 `OutOfProcess` 来切换到进程外托管模型。</span><span class="sxs-lookup"><span data-stu-id="29a69-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="29a69-215">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="29a69-215">502.5 Process Failure</span></span>

<span data-ttu-id="29a69-216">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-216">The worker process fails.</span></span> <span data-ttu-id="29a69-217">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-217">The app doesn't start.</span></span>

<span data-ttu-id="29a69-218">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="29a69-219">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="29a69-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="29a69-220">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29a69-221">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="29a69-222"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="29a69-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="29a69-223">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="29a69-224">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="29a69-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="29a69-225">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="29a69-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="29a69-226">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="29a69-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="29a69-227">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="29a69-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="29a69-228">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="29a69-229">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="29a69-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="29a69-230">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="29a69-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="29a69-231">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="29a69-232">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="29a69-233">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="29a69-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="29a69-234">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="29a69-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="29a69-235">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="29a69-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="29a69-236">连接重置</span><span class="sxs-lookup"><span data-stu-id="29a69-236">Connection reset</span></span>

<span data-ttu-id="29a69-237">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="29a69-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="29a69-238">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="29a69-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="29a69-239">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="29a69-240">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="29a69-241">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="29a69-241">Default startup limits</span></span>

<span data-ttu-id="29a69-242">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="29a69-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="29a69-243">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="29a69-244">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="29a69-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="29a69-245">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="29a69-246">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="29a69-247">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="29a69-248">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="29a69-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="29a69-249">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="29a69-250">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="29a69-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="29a69-251">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="29a69-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="29a69-252">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="29a69-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="29a69-253">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="29a69-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="29a69-254">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-255">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-256">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-257">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-258">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-259">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="29a69-260">检查日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-260">Examine the log.</span></span> <span data-ttu-id="29a69-261">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="29a69-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="29a69-262">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="29a69-263">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-264">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="29a69-265">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-266">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-267">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-268">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="29a69-269">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="29a69-270">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="29a69-271">运行应用：</span><span class="sxs-lookup"><span data-stu-id="29a69-271">Run the app:</span></span>
   * <span data-ttu-id="29a69-272">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="29a69-273">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="29a69-274">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-275">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-276">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-278">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-279">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="29a69-280">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="29a69-281">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-281">**Current release**</span></span>

* <span data-ttu-id="29a69-282">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="29a69-283">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="29a69-284">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="29a69-285">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="29a69-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="29a69-286">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-287">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-288">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-290">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-291">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="29a69-292">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="29a69-293">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="29a69-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="29a69-294">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-295">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-296">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="29a69-297">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="29a69-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="29a69-298">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-299">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-300">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-301">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-302">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-303">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-303">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-304">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="29a69-304">Return to the Azure portal.</span></span> <span data-ttu-id="29a69-305">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-306">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-307">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-308">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-309">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-310">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="29a69-311">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="29a69-312">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-313">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="29a69-314">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="29a69-315">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-316">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="29a69-317">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-318">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-319">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="29a69-320">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="29a69-321">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-322">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="29a69-323">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="29a69-324">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="29a69-325">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-326">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="29a69-327">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="29a69-328">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-328">Redeploy the app.</span></span>
   * <span data-ttu-id="29a69-329">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="29a69-330">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-331">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-332">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="29a69-333">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="29a69-334">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-335">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="29a69-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="29a69-336">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="29a69-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="29a69-337">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="29a69-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="29a69-338">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-339">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-340">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-341">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-342">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-343">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="29a69-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="29a69-344">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="29a69-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="29a69-345">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="29a69-346">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="29a69-347">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="29a69-348">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="29a69-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="29a69-349">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="29a69-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="29a69-350">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-350">Save the file.</span></span>

<span data-ttu-id="29a69-351">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="29a69-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-352">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-353">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-353">There's no limit on log file size.</span></span> <span data-ttu-id="29a69-354">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="29a69-355">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-356">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="29a69-357">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="29a69-358">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="29a69-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="29a69-359">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-360">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="29a69-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="29a69-361">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="29a69-361">Monitoring blades</span></span>

<span data-ttu-id="29a69-362">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="29a69-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="29a69-363">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="29a69-364">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="29a69-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="29a69-365">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="29a69-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="29a69-366">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="29a69-367">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="29a69-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="29a69-368">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="29a69-369">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="29a69-370">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="29a69-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="29a69-371">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="29a69-372">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="29a69-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="29a69-373">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="29a69-374">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-375">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-376">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-377">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-378">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="29a69-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-379">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-380">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-381">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="29a69-382">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="29a69-383">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="29a69-384">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="29a69-385">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="29a69-386">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="29a69-387">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="29a69-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="29a69-388">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-388">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-389">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="29a69-390">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="29a69-391">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="29a69-392">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-393">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="29a69-394">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="29a69-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="29a69-395">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="29a69-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-396">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-397">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-398">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-399">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="29a69-400">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="29a69-401">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="29a69-402">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="29a69-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="29a69-403">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="29a69-404">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="29a69-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="29a69-405">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="29a69-406">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="29a69-407">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="29a69-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="29a69-408">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-408">Run the app at a command prompt</span></span>

<span data-ttu-id="29a69-409">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-410">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="29a69-411">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="29a69-411">Framework-dependent deployment</span></span>

<span data-ttu-id="29a69-412">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="29a69-413">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="29a69-414">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="29a69-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="29a69-415">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-416">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-417">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-418">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="29a69-419">独立部署</span><span class="sxs-lookup"><span data-stu-id="29a69-419">Self-contained deployment</span></span>

<span data-ttu-id="29a69-420">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="29a69-421">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="29a69-422">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="29a69-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="29a69-423">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-424">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-425">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-426">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="29a69-427">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="29a69-428">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-429">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-430">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="29a69-431">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="29a69-432">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-432">Edit the *web.config* file.</span></span> <span data-ttu-id="29a69-433">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="29a69-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="29a69-434">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="29a69-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="29a69-435">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="29a69-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="29a69-436">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="29a69-437">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="29a69-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="29a69-438">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-439">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-439">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-440">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="29a69-441">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="29a69-442">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-442">Study the log for errors.</span></span>

<span data-ttu-id="29a69-443">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-444">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-445">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-446">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-446">Save the file.</span></span>

<span data-ttu-id="29a69-447">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-448">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-449">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-450">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-451">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="29a69-452">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="29a69-453">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="29a69-454">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="29a69-455">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="29a69-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="29a69-456">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="29a69-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="29a69-457">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="29a69-458">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="29a69-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="29a69-459">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="29a69-460">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="29a69-461">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="29a69-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="29a69-462">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="29a69-462">Obtain data from an app</span></span>

<span data-ttu-id="29a69-463">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="29a69-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="29a69-464">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="29a69-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="29a69-465">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="29a69-466">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="29a69-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="29a69-467">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="29a69-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="29a69-468">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="29a69-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="29a69-469">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="29a69-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="29a69-470">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="29a69-471">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-472">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="29a69-473">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="29a69-474">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="29a69-475">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-476">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="29a69-477">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="29a69-478">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="29a69-479">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="29a69-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-480">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="29a69-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="29a69-481">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="29a69-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="29a69-482">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="29a69-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="29a69-483">分析转储</span><span class="sxs-lookup"><span data-stu-id="29a69-483">Analyze the dump</span></span>

<span data-ttu-id="29a69-484">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="29a69-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="29a69-485">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="29a69-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="29a69-486">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-486">Clear package caches</span></span>

<span data-ttu-id="29a69-487">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="29a69-488">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="29a69-489">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="29a69-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="29a69-490">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="29a69-491">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="29a69-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="29a69-492">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="29a69-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="29a69-493">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="29a69-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="29a69-494">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="29a69-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="29a69-495">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29a69-496">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="29a69-497">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-497">Azure documentation</span></span>

* [<span data-ttu-id="29a69-498">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="29a69-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="29a69-499">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="29a69-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="29a69-500">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="29a69-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="29a69-501">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="29a69-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="29a69-502">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="29a69-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="29a69-503">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="29a69-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="29a69-504">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-505">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="29a69-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="29a69-506">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="29a69-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="29a69-507">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="29a69-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="29a69-508">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="29a69-509">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="29a69-510">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="29a69-511">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="29a69-512">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="29a69-513">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="29a69-514">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="29a69-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="29a69-515">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="29a69-516">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="29a69-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="29a69-517">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="29a69-518">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="29a69-519">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="29a69-520">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="29a69-521">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="29a69-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="29a69-522">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="29a69-523">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="29a69-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="29a69-524">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="29a69-525">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="29a69-526">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-526">App startup errors</span></span>

<span data-ttu-id="29a69-527">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="29a69-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="29a69-528">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断   。</span><span class="sxs-lookup"><span data-stu-id="29a69-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="29a69-529">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="29a69-529">403.14 Forbidden</span></span>

<span data-ttu-id="29a69-530">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-530">The app fails to start.</span></span> <span data-ttu-id="29a69-531">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="29a69-532">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="29a69-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="29a69-533">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-534">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="29a69-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-535">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="29a69-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="29a69-536">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-536">Perform the following steps:</span></span>

1. <span data-ttu-id="29a69-537">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-538">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="29a69-539">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="29a69-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="29a69-540">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="29a69-541">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="29a69-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="29a69-542">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="29a69-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="29a69-543">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="29a69-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="29a69-544">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="29a69-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="29a69-545">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="29a69-545">500 Internal Server Error</span></span>

<span data-ttu-id="29a69-546">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="29a69-547">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="29a69-548">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="29a69-549">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="29a69-550">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="29a69-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="29a69-551">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="29a69-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="29a69-552">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="29a69-553">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="29a69-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="29a69-554">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-554">The worker process fails.</span></span> <span data-ttu-id="29a69-555">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-555">The app doesn't start.</span></span>

<span data-ttu-id="29a69-556">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)  。</span><span class="sxs-lookup"><span data-stu-id="29a69-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="29a69-557">检查：</span><span class="sxs-lookup"><span data-stu-id="29a69-557">Check that:</span></span>

* <span data-ttu-id="29a69-558">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="29a69-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="29a69-559">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="29a69-560">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="29a69-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="29a69-561">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-561">The worker process fails.</span></span> <span data-ttu-id="29a69-562">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-562">The app doesn't start.</span></span>

<span data-ttu-id="29a69-563">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到进程外托管请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="29a69-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="29a69-564">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll   。</span><span class="sxs-lookup"><span data-stu-id="29a69-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="29a69-565">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="29a69-565">502.5 Process Failure</span></span>

<span data-ttu-id="29a69-566">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-566">The worker process fails.</span></span> <span data-ttu-id="29a69-567">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-567">The app doesn't start.</span></span>

<span data-ttu-id="29a69-568">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="29a69-569">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="29a69-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="29a69-570">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29a69-571">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="29a69-572"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="29a69-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="29a69-573">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="29a69-574">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="29a69-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="29a69-575">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="29a69-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="29a69-576">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="29a69-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="29a69-577">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="29a69-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="29a69-578">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="29a69-579">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="29a69-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="29a69-580">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="29a69-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="29a69-581">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="29a69-582">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="29a69-583">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="29a69-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="29a69-584">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="29a69-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="29a69-585">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="29a69-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="29a69-586">连接重置</span><span class="sxs-lookup"><span data-stu-id="29a69-586">Connection reset</span></span>

<span data-ttu-id="29a69-587">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="29a69-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="29a69-588">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="29a69-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="29a69-589">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="29a69-590">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="29a69-591">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="29a69-591">Default startup limits</span></span>

<span data-ttu-id="29a69-592">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="29a69-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="29a69-593">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="29a69-594">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="29a69-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="29a69-595">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="29a69-596">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="29a69-597">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="29a69-598">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="29a69-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="29a69-599">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="29a69-600">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="29a69-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="29a69-601">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="29a69-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="29a69-602">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="29a69-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="29a69-603">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="29a69-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="29a69-604">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-605">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-606">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-607">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-608">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-609">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="29a69-610">检查日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-610">Examine the log.</span></span> <span data-ttu-id="29a69-611">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="29a69-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="29a69-612">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="29a69-613">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-614">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="29a69-615">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-616">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-617">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-618">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="29a69-619">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="29a69-620">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="29a69-621">运行应用：</span><span class="sxs-lookup"><span data-stu-id="29a69-621">Run the app:</span></span>
   * <span data-ttu-id="29a69-622">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="29a69-623">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="29a69-624">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-625">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-626">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-628">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-629">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="29a69-630">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="29a69-631">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-631">**Current release**</span></span>

* <span data-ttu-id="29a69-632">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="29a69-633">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="29a69-634">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="29a69-635">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="29a69-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="29a69-636">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-637">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-638">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-640">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-641">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="29a69-642">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="29a69-643">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="29a69-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="29a69-644">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-645">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-646">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="29a69-647">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="29a69-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="29a69-648">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-649">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-650">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-651">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-652">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-653">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-653">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-654">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="29a69-654">Return to the Azure portal.</span></span> <span data-ttu-id="29a69-655">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-656">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-657">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-658">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-659">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-660">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="29a69-661">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="29a69-662">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-663">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="29a69-664">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="29a69-665">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-666">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="29a69-667">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-668">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-669">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="29a69-670">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="29a69-671">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-672">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="29a69-673">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="29a69-674">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="29a69-675">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-676">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="29a69-677">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="29a69-678">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-678">Redeploy the app.</span></span>
   * <span data-ttu-id="29a69-679">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="29a69-680">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-681">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-682">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="29a69-683">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="29a69-684">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-685">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="29a69-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="29a69-686">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="29a69-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="29a69-687">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="29a69-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="29a69-688">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-689">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-690">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-691">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-692">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-693">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="29a69-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="29a69-694">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="29a69-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="29a69-695">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="29a69-696">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="29a69-697">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="29a69-698">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="29a69-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="29a69-699">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="29a69-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="29a69-700">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-700">Save the file.</span></span>

<span data-ttu-id="29a69-701">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="29a69-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-702">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-703">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-703">There's no limit on log file size.</span></span> <span data-ttu-id="29a69-704">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="29a69-705">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-706">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="29a69-707">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="29a69-708">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="29a69-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="29a69-709">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-710">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="29a69-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="29a69-711">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="29a69-711">Monitoring blades</span></span>

<span data-ttu-id="29a69-712">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="29a69-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="29a69-713">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="29a69-714">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="29a69-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="29a69-715">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="29a69-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="29a69-716">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="29a69-717">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="29a69-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="29a69-718">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="29a69-719">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="29a69-720">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="29a69-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="29a69-721">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="29a69-722">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="29a69-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="29a69-723">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="29a69-724">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-725">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-726">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-727">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-728">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="29a69-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-729">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-730">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-731">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="29a69-732">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="29a69-733">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="29a69-734">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="29a69-735">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="29a69-736">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="29a69-737">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="29a69-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="29a69-738">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-738">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-739">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="29a69-740">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="29a69-741">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="29a69-742">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-743">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="29a69-744">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="29a69-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="29a69-745">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="29a69-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-746">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-747">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-748">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-749">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="29a69-750">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="29a69-751">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="29a69-752">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="29a69-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="29a69-753">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="29a69-754">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="29a69-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="29a69-755">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="29a69-756">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="29a69-757">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="29a69-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="29a69-758">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-758">Run the app at a command prompt</span></span>

<span data-ttu-id="29a69-759">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-760">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="29a69-761">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="29a69-761">Framework-dependent deployment</span></span>

<span data-ttu-id="29a69-762">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="29a69-763">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="29a69-764">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="29a69-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="29a69-765">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-766">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-767">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-768">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="29a69-769">独立部署</span><span class="sxs-lookup"><span data-stu-id="29a69-769">Self-contained deployment</span></span>

<span data-ttu-id="29a69-770">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="29a69-771">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="29a69-772">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="29a69-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="29a69-773">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-774">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-775">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-776">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="29a69-777">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="29a69-778">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-779">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-780">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="29a69-781">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="29a69-782">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-782">Edit the *web.config* file.</span></span> <span data-ttu-id="29a69-783">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="29a69-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="29a69-784">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="29a69-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="29a69-785">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="29a69-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="29a69-786">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="29a69-787">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="29a69-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="29a69-788">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-789">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-789">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-790">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="29a69-791">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="29a69-792">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-792">Study the log for errors.</span></span>

<span data-ttu-id="29a69-793">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-794">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-795">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-796">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-796">Save the file.</span></span>

<span data-ttu-id="29a69-797">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-798">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-799">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-800">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-801">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="29a69-802">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="29a69-803">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="29a69-804">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="29a69-805">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="29a69-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="29a69-806">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="29a69-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="29a69-807">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="29a69-808">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="29a69-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="29a69-809">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="29a69-810">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="29a69-811">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="29a69-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="29a69-812">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="29a69-812">Obtain data from an app</span></span>

<span data-ttu-id="29a69-813">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="29a69-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="29a69-814">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="29a69-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="29a69-815">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="29a69-816">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="29a69-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="29a69-817">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="29a69-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="29a69-818">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="29a69-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="29a69-819">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="29a69-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="29a69-820">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="29a69-821">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-822">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="29a69-823">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="29a69-824">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="29a69-825">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-826">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="29a69-827">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="29a69-828">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="29a69-829">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="29a69-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-830">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="29a69-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="29a69-831">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="29a69-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="29a69-832">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="29a69-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="29a69-833">分析转储</span><span class="sxs-lookup"><span data-stu-id="29a69-833">Analyze the dump</span></span>

<span data-ttu-id="29a69-834">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="29a69-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="29a69-835">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="29a69-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="29a69-836">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-836">Clear package caches</span></span>

<span data-ttu-id="29a69-837">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="29a69-838">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="29a69-839">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="29a69-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="29a69-840">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="29a69-841">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="29a69-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="29a69-842">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="29a69-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="29a69-843">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="29a69-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="29a69-844">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="29a69-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="29a69-845">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29a69-846">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="29a69-847">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-847">Azure documentation</span></span>

* [<span data-ttu-id="29a69-848">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="29a69-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="29a69-849">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="29a69-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="29a69-850">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="29a69-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="29a69-851">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="29a69-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="29a69-852">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="29a69-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="29a69-853">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="29a69-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="29a69-854">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-855">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="29a69-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="29a69-856">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="29a69-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="29a69-857">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="29a69-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="29a69-858">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="29a69-859">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="29a69-860">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="29a69-861">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="29a69-862">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="29a69-863">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="29a69-864">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="29a69-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="29a69-865">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="29a69-866">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="29a69-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="29a69-867">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="29a69-868">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="29a69-869">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="29a69-870">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="29a69-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="29a69-871">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="29a69-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="29a69-872">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="29a69-873">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="29a69-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="29a69-874">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="29a69-875">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="29a69-876">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="29a69-876">App startup errors</span></span>

<span data-ttu-id="29a69-877">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="29a69-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="29a69-878">本地调试时出现的“502.5 - 进程失败”可以使用本主题中的建议进行诊断  。</span><span class="sxs-lookup"><span data-stu-id="29a69-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="29a69-879">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="29a69-879">403.14 Forbidden</span></span>

<span data-ttu-id="29a69-880">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-880">The app fails to start.</span></span> <span data-ttu-id="29a69-881">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="29a69-882">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="29a69-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="29a69-883">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-884">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="29a69-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="29a69-885">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确   。</span><span class="sxs-lookup"><span data-stu-id="29a69-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="29a69-886">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-886">Perform the following steps:</span></span>

1. <span data-ttu-id="29a69-887">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-888">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="29a69-889">确认部署中存在 web.config 文件，并且其内容正确  。</span><span class="sxs-lookup"><span data-stu-id="29a69-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="29a69-890">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="29a69-891">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径    。</span><span class="sxs-lookup"><span data-stu-id="29a69-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="29a69-892">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹  。</span><span class="sxs-lookup"><span data-stu-id="29a69-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="29a69-893">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="29a69-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="29a69-894">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="29a69-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="29a69-895">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="29a69-895">500 Internal Server Error</span></span>

<span data-ttu-id="29a69-896">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="29a69-897">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="29a69-898">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="29a69-899">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="29a69-900">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="29a69-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="29a69-901">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="29a69-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="29a69-902">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="29a69-903">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="29a69-903">502.5 Process Failure</span></span>

<span data-ttu-id="29a69-904">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-904">The worker process fails.</span></span> <span data-ttu-id="29a69-905">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="29a69-905">The app doesn't start.</span></span>

<span data-ttu-id="29a69-906">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="29a69-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="29a69-907">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="29a69-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="29a69-908">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29a69-909">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="29a69-910"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="29a69-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="29a69-911">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="29a69-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="29a69-912">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="29a69-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="29a69-913">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="29a69-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="29a69-914">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="29a69-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="29a69-915">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="29a69-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="29a69-916">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="29a69-917">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="29a69-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="29a69-918">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="29a69-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="29a69-919">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="29a69-920">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="29a69-921">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="29a69-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="29a69-922">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="29a69-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="29a69-923">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="29a69-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="29a69-924">连接重置</span><span class="sxs-lookup"><span data-stu-id="29a69-924">Connection reset</span></span>

<span data-ttu-id="29a69-925">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="29a69-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="29a69-926">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="29a69-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="29a69-927">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="29a69-928">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="29a69-929">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="29a69-929">Default startup limits</span></span>

<span data-ttu-id="29a69-930">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒  。</span><span class="sxs-lookup"><span data-stu-id="29a69-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="29a69-931">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="29a69-932">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="29a69-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="29a69-933">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="29a69-934">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="29a69-935">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="29a69-936">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="29a69-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="29a69-937">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="29a69-938">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="29a69-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="29a69-939">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="29a69-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="29a69-940">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="29a69-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="29a69-941">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="29a69-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="29a69-942">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-943">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-944">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-945">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-946">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-947">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="29a69-948">检查日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-948">Examine the log.</span></span> <span data-ttu-id="29a69-949">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="29a69-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="29a69-950">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="29a69-951">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-952">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="29a69-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="29a69-953">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="29a69-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="29a69-954">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-955">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-956">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="29a69-957">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="29a69-958">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="29a69-959">运行应用：</span><span class="sxs-lookup"><span data-stu-id="29a69-959">Run the app:</span></span>
   * <span data-ttu-id="29a69-960">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="29a69-961">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="29a69-962">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-963">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-964">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-966">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-967">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="29a69-968">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="29a69-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="29a69-969">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="29a69-969">**Current release**</span></span>

* <span data-ttu-id="29a69-970">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="29a69-971">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="29a69-972">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="29a69-973">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="29a69-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="29a69-974">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="29a69-975">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="29a69-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="29a69-976">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="29a69-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="29a69-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="29a69-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="29a69-978">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="29a69-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="29a69-979">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="29a69-980">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="29a69-981">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="29a69-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="29a69-982">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-983">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-984">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="29a69-985">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮    。</span><span class="sxs-lookup"><span data-stu-id="29a69-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="29a69-986">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="29a69-987">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-988">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-989">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-990">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-991">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-991">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-992">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="29a69-992">Return to the Azure portal.</span></span> <span data-ttu-id="29a69-993">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-994">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-995">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-996">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-997">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="29a69-998">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="29a69-999">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="29a69-1000">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-1001">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="29a69-1002">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="29a69-1003">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-1004">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="29a69-1005">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-1006">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-1007">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="29a69-1008">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="29a69-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="29a69-1009">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-1010">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="29a69-1011">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="29a69-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="29a69-1012">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="29a69-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="29a69-1013">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-1014">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="29a69-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="29a69-1015">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="29a69-1015">Monitoring blades</span></span>

<span data-ttu-id="29a69-1016">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="29a69-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="29a69-1017">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="29a69-1018">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="29a69-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="29a69-1019">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="29a69-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="29a69-1020">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="29a69-1021">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="29a69-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="29a69-1022">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="29a69-1023">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="29a69-1024">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="29a69-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="29a69-1025">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="29a69-1026">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="29a69-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="29a69-1027">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29a69-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="29a69-1028">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="29a69-1029">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="29a69-1030">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="29a69-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="29a69-1031">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="29a69-1032">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件    。</span><span class="sxs-lookup"><span data-stu-id="29a69-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="29a69-1033">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="29a69-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-1034">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="29a69-1035">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="29a69-1036">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="29a69-1037">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="29a69-1038">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="29a69-1039">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="29a69-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="29a69-1040">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="29a69-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="29a69-1041">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="29a69-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="29a69-1042">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-1043">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="29a69-1044">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="29a69-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="29a69-1045">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="29a69-1046">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="29a69-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="29a69-1047">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="29a69-1048">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="29a69-1049">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-1050">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-1051">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-1052">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-1053">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="29a69-1054">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="29a69-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="29a69-1055">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="29a69-1056">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="29a69-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="29a69-1057">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="29a69-1058">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="29a69-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="29a69-1059">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="29a69-1060">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="29a69-1061">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="29a69-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="29a69-1062">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="29a69-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="29a69-1063">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="29a69-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29a69-1064">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="29a69-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="29a69-1065">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="29a69-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="29a69-1066">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="29a69-1067">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="29a69-1068">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="29a69-1069">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-1070">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-1071">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-1072">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="29a69-1073">独立部署</span><span class="sxs-lookup"><span data-stu-id="29a69-1073">Self-contained deployment</span></span>

<span data-ttu-id="29a69-1074">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="29a69-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="29a69-1075">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="29a69-1076">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="29a69-1077">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="29a69-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29a69-1078">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29a69-1079">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29a69-1080">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="29a69-1081">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="29a69-1082">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a69-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29a69-1083">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29a69-1084">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="29a69-1085">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="29a69-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="29a69-1086">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="29a69-1087">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="29a69-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="29a69-1088">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="29a69-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="29a69-1089">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="29a69-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="29a69-1090">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="29a69-1091">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="29a69-1092">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29a69-1093">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="29a69-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="29a69-1094">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="29a69-1095">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="29a69-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="29a69-1096">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="29a69-1096">Study the log for errors.</span></span>

<span data-ttu-id="29a69-1097">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="29a69-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="29a69-1098">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="29a69-1099">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29a69-1100">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1100">Save the file.</span></span>

<span data-ttu-id="29a69-1101">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="29a69-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-1102">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29a69-1103">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="29a69-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29a69-1104">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="29a69-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29a69-1105">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="29a69-1106">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="29a69-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="29a69-1107">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="29a69-1108">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="29a69-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="29a69-1109">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="29a69-1110">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="29a69-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="29a69-1111">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="29a69-1112">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="29a69-1112">Obtain data from an app</span></span>

<span data-ttu-id="29a69-1113">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="29a69-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="29a69-1114">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="29a69-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="29a69-1115">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="29a69-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="29a69-1116">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="29a69-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="29a69-1117">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="29a69-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="29a69-1118">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="29a69-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="29a69-1119">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="29a69-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="29a69-1120">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="29a69-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="29a69-1121">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-1122">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="29a69-1123">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="29a69-1124">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="29a69-1125">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="29a69-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="29a69-1126">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="29a69-1127">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="29a69-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="29a69-1128">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="29a69-1129">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="29a69-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="29a69-1130">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="29a69-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="29a69-1131">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="29a69-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="29a69-1132">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="29a69-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="29a69-1133">分析转储</span><span class="sxs-lookup"><span data-stu-id="29a69-1133">Analyze the dump</span></span>

<span data-ttu-id="29a69-1134">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="29a69-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="29a69-1135">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="29a69-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="29a69-1136">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="29a69-1136">Clear package caches</span></span>

<span data-ttu-id="29a69-1137">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="29a69-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="29a69-1138">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="29a69-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="29a69-1139">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="29a69-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="29a69-1140">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="29a69-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="29a69-1141">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="29a69-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="29a69-1142">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="29a69-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="29a69-1143">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="29a69-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="29a69-1144">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="29a69-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="29a69-1145">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="29a69-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29a69-1146">其他资源</span><span class="sxs-lookup"><span data-stu-id="29a69-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="29a69-1147">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-1147">Azure documentation</span></span>

* [<span data-ttu-id="29a69-1148">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="29a69-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="29a69-1149">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="29a69-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="29a69-1150">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="29a69-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="29a69-1151">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="29a69-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="29a69-1152">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="29a69-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="29a69-1153">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="29a69-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="29a69-1154">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="29a69-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="29a69-1155">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="29a69-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="29a69-1156">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="29a69-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="29a69-1157">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="29a69-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="29a69-1158">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="29a69-1159">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="29a69-1160">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a69-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="29a69-1161">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="29a69-1162">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="29a69-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="29a69-1163">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="29a69-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
