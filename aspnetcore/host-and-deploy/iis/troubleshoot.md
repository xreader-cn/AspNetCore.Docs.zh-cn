---
title: 对 IIS 上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core 应用的 Internet Information Services (IIS) 部署的问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: cb42a262c89c27fa350e936184f8ddb3a02788f0
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034738"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="25ebd-103">对 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="25ebd-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="25ebd-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25ebd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="25ebd-105">本文说明了如何在使用 [Internet Information Services (IIS)](/iis) 托管时诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="25ebd-106">本文提供的信息适用于在 Windows Server 和 Windows 桌面上的 IIS 中托管。</span><span class="sxs-lookup"><span data-stu-id="25ebd-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="25ebd-107">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="25ebd-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="25ebd-108">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行故障排除   。</span><span class="sxs-lookup"><span data-stu-id="25ebd-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="25ebd-109">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="25ebd-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="25ebd-110">可以使用本主题中的建议对本地调试进行故障排除时，出现“502.5 进程失败”  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="25ebd-111">其他故障排除主题：</span><span class="sxs-lookup"><span data-stu-id="25ebd-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="25ebd-112"><xref:host-and-deploy/azure-apps/troubleshoot> 虽然应用服务使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)和 IIS 托管应用，但若要获取特定于应用服务的说明，请参阅专用主题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="25ebd-113"><xref:fundamentals/error-handling> 了解如何在本地系统上处理 ASP.NET Core 应用在开发期间的错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="25ebd-114">[了解如何使用 Visual Studio 进行调试](/visualstudio/debugger/getting-started-with-the-debugger)本主题介绍了 Visual Studio 调试器的功能。</span><span class="sxs-lookup"><span data-stu-id="25ebd-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="25ebd-115">[使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/editor/debugging)了解 Visual Studio Code 中内置的调试支持。</span><span class="sxs-lookup"><span data-stu-id="25ebd-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="25ebd-116">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="25ebd-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="25ebd-117">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-117">502.5 Process Failure</span></span>

<span data-ttu-id="25ebd-118">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-118">The worker process fails.</span></span> <span data-ttu-id="25ebd-119">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-119">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-120">ASP.NET Core 模块尝试启动后端 dotnet 进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="25ebd-121">通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="25ebd-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="25ebd-122">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="25ebd-123">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="25ebd-124"> 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll  文件）。</span><span class="sxs-lookup"><span data-stu-id="25ebd-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="25ebd-125">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="25ebd-126">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="25ebd-127">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="25ebd-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="25ebd-129">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="25ebd-130">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-130">The worker process fails.</span></span> <span data-ttu-id="25ebd-131">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-131">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-132">ASP.NET Core 模块尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="25ebd-133">通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="25ebd-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="25ebd-134">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="25ebd-135">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="25ebd-136">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="25ebd-137">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-137">The worker process fails.</span></span> <span data-ttu-id="25ebd-138">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-138">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-139">ASP.NET Core 模块无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="25ebd-140">检查：</span><span class="sxs-lookup"><span data-stu-id="25ebd-140">Check that:</span></span>

* <span data-ttu-id="25ebd-141">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="25ebd-142">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="25ebd-143">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="25ebd-144">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-144">The worker process fails.</span></span> <span data-ttu-id="25ebd-145">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-145">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-146">ASP.NET Core 模块无法找到进程外托管请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="25ebd-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="25ebd-147">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll   。</span><span class="sxs-lookup"><span data-stu-id="25ebd-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="25ebd-148">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="25ebd-148">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="25ebd-149">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-149">The worker process fails.</span></span> <span data-ttu-id="25ebd-150">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-150">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-151">ASP.NET Core 模块尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-151">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="25ebd-152">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="25ebd-152">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="25ebd-153">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-153">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="25ebd-154">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="25ebd-154">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="25ebd-155">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-155">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="25ebd-156">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="25ebd-156">To fix this error, either:</span></span>

* <span data-ttu-id="25ebd-157">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="25ebd-157">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="25ebd-158">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="25ebd-158">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="25ebd-159">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="25ebd-159">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="25ebd-160">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-160">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="25ebd-161">还可以在[应用程序事件日志](#application-event-log)中找到进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="25ebd-161">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="25ebd-162">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="25ebd-162">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="25ebd-163">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-163">The worker process fails.</span></span> <span data-ttu-id="25ebd-164">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-164">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-165">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-165">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="25ebd-166">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-166">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="25ebd-167">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="25ebd-167">To fix this error, either:</span></span>

* <span data-ttu-id="25ebd-168">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="25ebd-168">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="25ebd-169">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="25ebd-169">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="25ebd-170">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-170">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="25ebd-171">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-171">The worker process fails.</span></span> <span data-ttu-id="25ebd-172">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-172">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-173">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="25ebd-173">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="25ebd-174">ASP.NET Core 模块只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-174">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="25ebd-175">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="25ebd-175">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="25ebd-176">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="25ebd-176">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="25ebd-177">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="25ebd-177">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="25ebd-178">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-178">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="25ebd-179">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-179">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="25ebd-180">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="25ebd-180">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="25ebd-181">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-181">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="25ebd-182">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-182">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="25ebd-183">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-183">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="25ebd-184">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻   。</span><span class="sxs-lookup"><span data-stu-id="25ebd-184">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="25ebd-185">这表示 ASP.NET Core 模块的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="25ebd-185">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="25ebd-186">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="25ebd-186">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="25ebd-187">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="25ebd-187">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="25ebd-188">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-188">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="25ebd-189">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="25ebd-189">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="25ebd-190">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-190">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="25ebd-191">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="25ebd-191">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="25ebd-192">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="25ebd-192">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="25ebd-193">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-193">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="25ebd-194">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-194">The worker process fails.</span></span> <span data-ttu-id="25ebd-195">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-195">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-196">ASP.NET Core 模块尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-196">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="25ebd-197">通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="25ebd-197">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="25ebd-198">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="25ebd-198">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="25ebd-199">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="25ebd-199">The worker process fails.</span></span> <span data-ttu-id="25ebd-200">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-200">The app doesn't start.</span></span>

<span data-ttu-id="25ebd-201">加载 ASP.NET Core 模块组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-201">An unknown error occurred loading ASP.NET Core Module components.</span></span> <span data-ttu-id="25ebd-202">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="25ebd-202">Take one of the following actions:</span></span>

* <span data-ttu-id="25ebd-203">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”）   。</span><span class="sxs-lookup"><span data-stu-id="25ebd-203">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="25ebd-204">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-204">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="25ebd-205">在 [GitHub 存储库](https://github.com/aspnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-205">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="25ebd-206">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="25ebd-206">500 Internal Server Error</span></span>

<span data-ttu-id="25ebd-207">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-207">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="25ebd-208">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-208">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="25ebd-209">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-209">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="25ebd-210">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="25ebd-210">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="25ebd-211">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="25ebd-211">From the server's perspective, that's correct.</span></span> <span data-ttu-id="25ebd-212">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="25ebd-212">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="25ebd-213">在服务器上[在命令提示符处运行应用](#run-the-app-at-a-command-prompt)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-213">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="25ebd-214">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="25ebd-214">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="25ebd-215">应用未能启动，因为应用的程序集 (.dll  ) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="25ebd-215">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="25ebd-216">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-216">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="25ebd-217">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="25ebd-217">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="25ebd-218">在 IIS 管理器的“应用程序池”  中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="25ebd-218">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="25ebd-219">在“操作”  面板中的“编辑应用程序池”  下选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-219">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="25ebd-220">设置“启用 32 位应用程序”  ：</span><span class="sxs-lookup"><span data-stu-id="25ebd-220">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="25ebd-221">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-221">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="25ebd-222">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-222">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="25ebd-223">连接重置</span><span class="sxs-lookup"><span data-stu-id="25ebd-223">Connection reset</span></span>

<span data-ttu-id="25ebd-224">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="25ebd-224">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="25ebd-225">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="25ebd-225">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="25ebd-226">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-226">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="25ebd-227">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-227">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="25ebd-228">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="25ebd-228">Default startup limits</span></span>

<span data-ttu-id="25ebd-229">ASP.NET Core 模块的默认“startupTimeLimit”  配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="25ebd-229">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="25ebd-230">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-230">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="25ebd-231">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-231">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="25ebd-232">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="25ebd-232">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="25ebd-233">启用 ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="25ebd-233">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="25ebd-234">将以下处理程序设置添加到应用的 web.config  文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="25ebd-234">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="25ebd-235">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="25ebd-235">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="25ebd-236">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="25ebd-236">Application Event Log</span></span>

<span data-ttu-id="25ebd-237">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="25ebd-237">Access the Application Event Log:</span></span>

1. <span data-ttu-id="25ebd-238">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-238">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="25ebd-239">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="25ebd-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="25ebd-240">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="25ebd-240">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="25ebd-241">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-241">Search for errors associated with the failing app.</span></span> <span data-ttu-id="25ebd-242">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="25ebd-242">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="25ebd-243">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="25ebd-243">Run the app at a command prompt</span></span>

<span data-ttu-id="25ebd-244">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="25ebd-244">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="25ebd-245">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="25ebd-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="25ebd-246">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="25ebd-246">Framework-dependent deployment</span></span>

<span data-ttu-id="25ebd-247">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="25ebd-247">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="25ebd-248">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-248">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="25ebd-249">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-249">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="25ebd-250">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="25ebd-250">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="25ebd-251">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-251">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="25ebd-252">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-252">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="25ebd-253">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-253">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="25ebd-254">独立部署</span><span class="sxs-lookup"><span data-stu-id="25ebd-254">Self-contained deployment</span></span>

<span data-ttu-id="25ebd-255">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="25ebd-255">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="25ebd-256">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="25ebd-256">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="25ebd-257">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-257">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="25ebd-258">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="25ebd-258">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="25ebd-259">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-259">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="25ebd-260">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-260">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="25ebd-261">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-261">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="25ebd-262">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="25ebd-262">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="25ebd-263">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="25ebd-263">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="25ebd-264">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="25ebd-264">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="25ebd-265">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="25ebd-265">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="25ebd-266">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-266">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="25ebd-267">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="25ebd-267">Edit the *web.config* file.</span></span> <span data-ttu-id="25ebd-268">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="25ebd-268">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="25ebd-269">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="25ebd-269">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="25ebd-270">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="25ebd-270">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="25ebd-271">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-271">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="25ebd-272">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-272">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="25ebd-273">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="25ebd-273">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="25ebd-274">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="25ebd-274">Make a request to the app.</span></span>
1. <span data-ttu-id="25ebd-275">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="25ebd-275">Navigate to the *logs* folder.</span></span> <span data-ttu-id="25ebd-276">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="25ebd-276">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="25ebd-277">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-277">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="25ebd-278">故障排除完成后，禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="25ebd-278">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="25ebd-279">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="25ebd-279">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="25ebd-280">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-280">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="25ebd-281">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="25ebd-281">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="25ebd-282">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="25ebd-282">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="25ebd-283">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="25ebd-283">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="25ebd-284">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="25ebd-284">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="25ebd-285">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-285">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="25ebd-286">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="25ebd-286">Enable the Developer Exception Page</span></span>

<span data-ttu-id="25ebd-287">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-287">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="25ebd-288">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="25ebd-288">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="25ebd-289">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="25ebd-289">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="25ebd-290">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="25ebd-290">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="25ebd-291">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-291">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="25ebd-292">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="25ebd-292">Common startup errors</span></span>

<span data-ttu-id="25ebd-293">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="25ebd-293">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="25ebd-294">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="25ebd-294">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="25ebd-295">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="25ebd-295">Obtain data from an app</span></span>

<span data-ttu-id="25ebd-296">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="25ebd-296">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="25ebd-297">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="25ebd-297">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="25ebd-298">创建转储</span><span class="sxs-lookup"><span data-stu-id="25ebd-298">Create a dump</span></span>

<span data-ttu-id="25ebd-299">转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="25ebd-299">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="25ebd-300">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="25ebd-300">App crashes or encounters an exception</span></span>

<span data-ttu-id="25ebd-301">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="25ebd-301">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="25ebd-302">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-302">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="25ebd-303">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="25ebd-303">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="25ebd-304">运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="25ebd-304">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="25ebd-305">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="25ebd-305">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="25ebd-306">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="25ebd-306">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="25ebd-307">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-307">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="25ebd-308">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="25ebd-308">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="25ebd-309">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="25ebd-309">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="25ebd-310">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="25ebd-310">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="25ebd-311">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-311">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="25ebd-312">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="25ebd-312">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="25ebd-313">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="25ebd-313">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="25ebd-314">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="25ebd-314">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="25ebd-315">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="25ebd-315">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="25ebd-316">分析转储</span><span class="sxs-lookup"><span data-stu-id="25ebd-316">Analyze the dump</span></span>

<span data-ttu-id="25ebd-317">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="25ebd-317">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="25ebd-318">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-318">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="25ebd-319">远程调试</span><span class="sxs-lookup"><span data-stu-id="25ebd-319">Remote debugging</span></span>

<span data-ttu-id="25ebd-320">请参阅 Visual Studio 文档中的[在 Visual Studio 2017 中远程调试远程 IIS 计算机上的 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-320">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="25ebd-321">Application Insights</span><span class="sxs-lookup"><span data-stu-id="25ebd-321">Application Insights</span></span>

<span data-ttu-id="25ebd-322">[Application Insights](/azure/application-insights/) 提供来自 IIS 托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="25ebd-322">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="25ebd-323">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="25ebd-323">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="25ebd-324">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="25ebd-324">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="25ebd-325">其他建议</span><span class="sxs-lookup"><span data-stu-id="25ebd-325">Additional advice</span></span>

<span data-ttu-id="25ebd-326">有时，正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内升级包版本后立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="25ebd-326">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="25ebd-327">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="25ebd-327">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="25ebd-328">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="25ebd-328">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="25ebd-329">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="25ebd-329">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="25ebd-330">清除 %UserProfile%\\.nuget\\packages  和 %LocalAppData%\\Nuget\\v3-cache  中的包缓存。</span><span class="sxs-lookup"><span data-stu-id="25ebd-330">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="25ebd-331">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="25ebd-331">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="25ebd-332">确认在重新部署应用之前已完全删除服务器上的先前部署。</span><span class="sxs-lookup"><span data-stu-id="25ebd-332">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="25ebd-333">清除包缓存的简便方法是从命令提示符执行 `dotnet nuget locals all --clear`。</span><span class="sxs-lookup"><span data-stu-id="25ebd-333">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="25ebd-334">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="25ebd-334">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="25ebd-335">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="25ebd-335">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25ebd-336">其他资源</span><span class="sxs-lookup"><span data-stu-id="25ebd-336">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
